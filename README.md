## Topcoder Platform Pages
Welcome to Topcoder Platform pages. Here you'll find our style guides, deployment documention to build and maintain our apps.

#### Development Guides

[Services](pages/dev-guides/services/services.md)

[React Web Apps](pages/dev-guides/webapps/webapps.md)

#### Deployment Guides
HowTos on deploying services and front end applications using CI/CD best practices to our infrastructure on AWS. Covers deploying services to AWS Elastic Beanstalk, AWS ECS.

* [CircleCI + AWS Elastic Container Service](pages/deploys/circleci-aws-ecs.md)
* CircleCI + AWS Elastic Beanstalk - Pending


#### Repositories
**Web Apps**
* [topcoder-app](https://github.com/appirio-tech/topcoder-app): Community facing webapp
* [connect-app](https://github.com/appirio-tech/connect-app): Customer facing webapp


**Services**
* [tc-api](https://github.com/appirio-tech/tc-api): Node.js app supporting Topcoder V2 API
* [ap-challenge-microservice](https://github.com/appirio-tech/ap-challenge-microservice): Microservice handling CRUD operations for challenges
* [ap-member-microservice](https://github.com/appirio-tech/ap-member-microservice): Microservice for CRUD operations for Topcoder community members
* [tc-api-core](https://github.com/appirio-tech/tc1-api-core): Microservice managing identity, login, registration for all Topcoder Users
* [ap-notification-service](https://github.com/appirio-tech/ap-notification-service): Service handling transactional emails being sent through SendGrid.
* [ap-review-microservice](https://github.com/appirio-tech/ap-review-microservice): Microservice handling CRUD operations for challenge reviews
* [ap-submission-microservice](https://github.com/appirio-tech/ap-submission-microservice): Microservice handling operations for challenge submissions
* [ap-submission-processor](https://github.com/appirio-tech/ap-submission-processor): Service that handles processing of submissions once they are uploaded to S3.
* [ap-file-microservice](https://github.com/appirio-tech/ap-file-microservice): Microservice handling operations for file access, upload etc.
* [tc-direct-project-service](https://github.com/appirio-tech/tc-direct-project-service): Microservice handling operations for direct projects.
* [tc-project-service](https://github.com/topcoder-platform/tc-project-service): Microservice handling CRUD operations for Connect Projects
* [tc-tags-service](https://github.com/appirio-tech/tc-tags-service): Microservice handling CRUD operations for tags
* [tc-message-service](https://github.com/topcoder-platform/tc-message-service): Microservice exposing APIs to interact with Discourse
* [tc-connect-notifications](https://github.com/topcoder-platform/tc-connect-notifications): Service orchestrating notifications to / from connect users, and system (CoderBot)
