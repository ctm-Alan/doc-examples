# How to contribute

Have some up front discussions about which services you will need to make changes to prior to you submitting pull requests to our repositories.

The best place to start such a discussion is in our [Public Slack channel].

# Reporting Bugs
All bugs should be reported in our [Public Slack channel]. If we determine it is genuine we will open a card in JIRA in order to track it.

# Code Contributions

## Smaller is better - one issue per PR

Small pull requests, like updating configuration, fixing minor bugs or correcting spelling mistakes don't take long to review and the turn around for such changes should be pretty quick. Anything larger than that will require significant time to review and should have been worked into our roadmap prior to you making these changes.

## Getting the source code

While everyone has read only access to our repositories, if you are not on our team we expect you to fork our codebases in order to raise pull requests. If you've never forked before you should read the github documentation for [forking a repo](https://help.github.com/articles/fork-a-repo/).

## Commits / branching

Always fetch the latest code before branching. Branch off the latest master.

Ideally your branch should be named after the JIRA issue. This allows JIRA plugins to automatically detect relevant branches per issue. e.g. `mtm-1928-add-reference-data-for-energy`

Your commit messages on the branch should also start with the JIRA issue. This allows our release process to tag which issues are being released.

You may want to delete your branch after your PR has been merged to keep your fork clean.

## Building and Running our Code

Most of our projects are built with .NET Core and as long as you have the correct version of the SDK installed you should be able to build as per normal. We don't use docker locally, so just run the project directly - no need to spin up containers locally.

We use Rider on M1 Mac and that is the use case we've optimized for, you may find that some other IDE's may act and behave differently when it comes to building and running our projects which we haven't catered for.

Generally we don't spin up containers representing other services locally. You can talk to test/shadow instances of the service dependencies when debugging locally.

## Submitting a Pull Request

Run all the tests locally first.

Match the style of code for the project you're working on.

Limit your changes to the product you're working on in order to limit the impact to other products sharing the same code base.

Introducing framework changes and adding additional dependencies is generally discouraged and should be discussed beforehand and raised as a separate pull request.

Pull requests in general should be done against the `main` branch.

We expect another developer from your team to have gone over your pull request and "approved" it on github beforehand to make sure there are no spelling mistakes, code formatting mistakes and that it complies with our contributing guidelines.

When you're ready for us to review you should post it in our [Public Slack channel].

## Pull Request Format

Each pull request must have a matching issue in JIRA. The JIRA issue key should be at the start of the title of the pull request. The title should contain a short description of the work / solution.

The description should start with a clickable link to your JIRA card. It should then have a brief description about the work done in terms of your approach. There is no need to reproduce the problem statement from JIRA.

If the PR is big (it shouldnt be), please split up the commits in a way that makes it easier for us to understand the changes. This is especially important if you are doing large scale renaming, file moving, namespace changes etc.

If your work is not ready to be merged and released to production, you should add "* DO NOT MERGE *" to the title of your pull request. You can also raise a draft PR - we don't tend to comment on these. But if you'd like us to, feel free to ask in #micro-machines-public


If your code relies on other services or any dependencies (such as reference data collections or event sink events), please ensure that they have been released beforehand, otherwise you should add "* DO NOT MERGE *" to the title. Let us know so we can bear that in mind when accepting your changes.

## Testing
Any new code or bug fix should have tests attached to it.

Match the existing testing strategy.

Limit the number of integration tests and have most cases covered by unit testing as localised as possible.

There are some things that are either difficult to test and/or aren't worth testing and we'll use our own discretion in those cases.

If making changes to our scripts (bash etc.) we don't expect any tests but we do expect you to attempt to manually test them beforehand if possible.

## Deployment
Deployment to the lower environments is automatic - we have some smoke tests which run to make sure everything still works. But if it is all ok, you will soon see your changes in our non-Prod environments. 

For deployment to production, we're unlikely to schedule a deployment specifically for you - it'll instead be bundled up into one of our fairly regular releases. If you have a specific deadline, or sequence in mind - drop a message into #micro-machines-public. Scheduling a release as required is pretty easy and we don't mind. We prefer to deploy Mon-Thu 7-10am though.

## Additions to the HTTP API

When your change adds routes to the service's HTTP API, then we ask that you also:
* Get approval via our public Slack channel ([#micro-machines-public](slack://channel?id=C1F2ADXGF&team=T029V69AG))
* Configure our load test to include your new routes:
   * Update our [locust script](https://github.com/ComparetheMarket/product.journey-gateway/blob/master/loadtesting/locustfile.py) to include your new routes.
   * Update our [load test weightings file](https://github.com/ComparetheMarket/product.journey-gateway/blob/master/loadtesting/loadtesting.csv) to include an estimate of the amount of load the routes will receive during a busy period (4 hours on a Monday evening between 18:00 - 22:00).
* Add relevant [swagger/producer tests](https://github.com/ComparetheMarket/product.journey-gateway/tree/master/contract-tests/producer) to ensure that our automatically-generated swagger file stays up-to-date.
* Conform to the CtM policy of penetration testing new routes:
   * Arrange a penetration test of the new route(s).
   * Include a feature toggle so that initially the routes are only enabled in non-production.
   * Once the penetration test has been completed in shadow, raise a separate PR to remove the feature toggle and enable the routes in all environments.
