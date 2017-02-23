# CircleCI to AWS - ECS

This doc covers deployments from CircleCI to AWS - ECS

Please refer to CircleCI for documentation on CI process CircleCI - https://circleci.com/docs/

### Deploy to AWS ECS via CircleCI (using ECR)
Scripts required to build, test a dockerized application on CircleCI, push to an AWS EC2 Container Registry and deploy to ECS cluster
#### Prerequisites
An EC2 Container Service cluster & Container Registry must already be setup on AWS.

**Environment Variables to be configured in CircleCI**
- AWS_ACCOUNT_ID
- AWS_ACCESS_KEY_ID
- AWS_SECRET_ACCESS_KEY
- AWS_ECS_CLUSTER: *Can be hard-coded in your deploy script*
- AWS_ECS_SERVICE *Can be hard-coded in your deploy script*
- AWS_REGION
- Additional configuration data that your service might require, such as API Keys etc. *Note: Maybe a configuration management system such as [Consul](https://www.consul.io/intro/) might be more appropriate for your use case*

**Note**: You can add an Node Env prefix to your ENV variables if your values vary per Environment

### Deploy script(s)
#### ./circle.yml
```

deployment:
  development:
    branch: dev
    owner: topcoder-platform
    commands:
      - docker build -t $DEV_AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$AWS_REPOSITORY:$CIRCLE_SHA1 .
      - ./deploy_ecs.sh __YOUR_TASK_NAME__ $CIRCLE_SHA1 DEV

  production:
    tag: /v[0-9]+(\.[0-9]+)*/
    owner: topcoder-platform
    commands:
      - docker build -t $PROD_AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$AWS_REPOSITORY:$CIRCLE_TAG .
      - ./deploy_ecs.sh __YOUR_TASK_NAME__ $CIRCLE_TAG PROD
```
Production environment can be configured to trigger deploys once a release tag has been created. ECR images will be tagged with CircleTag in this case.

#### ./deploy_ecs.sh
This is a template file. You most likely will need to modify `make_task_def()` to suit your needs.
```
#!/usr/bin/env bash

# more bash-friendly output for jq
JQ="jq --raw-output --exit-status"

TASK_NAME = $1
IMAGE_TAG=$2
ENV=$3
ACCOUNT_ID=$(eval "echo \$${ENV}_AWS_ACCOUNT_ID")

configure_aws_cli() {
	export AWS_ACCESS_KEY_ID=$(eval "echo \$${ENV}_AWS_ACCESS_KEY_ID")
	export AWS_SECRET_ACCESS_KEY=$(eval "echo \$${ENV}_AWS_SECRET_ACCESS_KEY")
	aws --version
	aws configure set default.region $AWS_REGION
	aws configure set default.output json
}

deploy_cluster() {

    family=$TASK_NAME

    make_task_def
    register_definition
    if [[ $(aws ecs update-service --cluster $AWS_ECS_CLUSTER --service $AWS_ECS_SERVICE --task-definition $revision | \
                   $JQ '.service.taskDefinition') != $revision ]]; then
        echo "Error updating service."
        return 1
    fi

    echo "Deployed!"
    return 0
}

make_task_def(){
	task_template='[
		{
			"name": "%s",
			"image": "%s",
			"essential": true,
			"memory": 200,
			"cpu": 10,
			"environment": [
			  #... ADD YOUR ENVIRONMENT VARIABLES HERE...
				{
					"name": "NODE_ENV",
					"value": "%s"
				},
				{
					"name": "LOG_LEVEL",
					"value": "%s"
				},
			## ....
			]
		}
	]'

	image = "$ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$AWS_REPOSITORY:$IMAGE_TAG"

	# If using different values per env, map them here..
	log_level=$(eval "echo \$${ENV}_LOG_LEVEL")
	if [ "$ENV" = "PROD" ]; then
		node_env=production
	elif [ "$ENV" = "DEV" ]; then
		node_env=development
	fi

	task_def=$(printf "$task_template" $image $node_env $log_level ... ADDITIONAL ENV PARAMS... )
}

push_ecr_image(){
	eval $(aws ecr get-login --region $AWS_REGION)
	docker push $IMAGE_NAME
}

register_definition() {

    if revision=$(aws ecs register-task-definition --container-definitions "$task_def" --family $family | $JQ '.taskDefinition.taskDefinitionArn'); then
        echo "Revision: $revision"
    else
        echo "Failed to register task definition"
        return 1
    fi

}

configure_aws_cli
push_ecr_image
deploy_cluster

```
#### References:
- [AmazonECS](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)
- [Continuous Deployment with AWS EC2 Container Service
](https://circleci.com/docs/continuous-deployment-with-aws-ec2-container-service/)
