## OpenFaaS Cloud Architecture

The OpenFaaS Cloud Architecture in made up of several modules:

* A CI/CD pipeline made up of a number of OpenFaaS functions
* A dashboard (served through OpenFaaS)
* A container builder (buildkit)
* An authentication service (edge-auth)
* An edge-router used to map user sub-domains to endpoints (edge-router)

### Conceptual diagram

[![](https://github.com/openfaas/openfaas-cloud/raw/master/docs/conceptual-overview.png)](https://github.com/openfaas/openfaas-cloud/raw/master/docs/conceptual-overview.png)

This conceptual diagram shows how OpenFaaS Cloud integrates with GitHub.com/GitLab through the use of an event-driven architecture.

Main use-cases:

* User pushes code - GitHub/GitLab push event is sent to github-event/gitlab-event function triggering a CI/CD workflow
* User removes GitHub/GitLab app from one or more repos - garbage collection is invoked removing 1-many functions
* User accesses function via router using "pretty URL" format and request is routed to function via API Gateway

See [COMPONENTS.md](https://github.com/openfaas/openfaas-cloud/blob/master/docs/COMPONENTS.md) in the code repository for more information.

### Appendix

#### Use your own custom CI solution

OpenFaaS Cloud offers built-in CI/CD, but you can also use your own custom CI solution to build container images, then use OpenFaaS Cloud to deploy them.

* 1) Create a source repository and commit your services here i.e. "service-source", do not add it to OpenFaaS Cloud
* 2) Integrate your "service-source" repo with your custom CI system, have it publish Docker images to your registry for instance as `registry/service-source-image-name:pushed-tag`
* 3) Create an infrastructure repository called "service-deployment"
* 4) In the "service-deployment" repo run `faas-cli new --lang dockerfile service-deployment`
* 5) Rename `service-deployment.yml` to `stack.yml`
* 6) Edit `service-deployment/Dockerfile` and replace it with one line: `FROM registry/service-source-image-name:pushed-tag`
* 7) Add OpenFaaS Cloud to the `service-deployment` repository

Now you have separate CI and CD, every time you want to deploy a new version of the "service", simply commit into the "service-deployment" repository and update the tag in the `FROM` line in `service-deployment/Dockerfile`.

You can also automate this process if you wish.
