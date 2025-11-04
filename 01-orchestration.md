# Orchestration 

Most production ML work revolves around doing stuff in a sequence where next steps depend on completion of previous steps. 

For example, to train a model, we might need to do the following:

1. Read some data from Big Query (or another data warehouse) into a file
2. Run some transformations on the data
3. Write data back to a Big Query table
4. Run some Big Query SQL statements on the data
5. Launch an ML training job using PyTorch
6. Write results to Model Registry
7. Write some output data (like batch recommendations or computed vectors/features) to Big Query
8. Transfer this data to a key-value store for serving

This needs to happen on a schedule - for example every Monday. 

Usually in any given AI/ML team, you will have tens if not hundreds of such pipelines that need to be run on various schedules. 

The tool to do this job is an orchestrator. 
Some well-known orchestrators are AirFlow, the more DevOps-y Argo Workflows, Dagster, Vertex AI Pipeline, Luigi (there are many more). 

## How to pick an orchestrator 

Remember, you'll have to operate this thing in production so write down some basic requirements.
These are some useful questions to guide you:

1. Will I have to support the infrastructure and deployment of the orchestrator myself or do I have a team that does that for me?
Do not underestimate the time required to maintain and debug an orchestrator. This should figure into your discussion of whether or
not you should pruchase or go with a managed solution or self-host an open source solution.

2. How will I be alerted of pipeline failures?

Does your team use PagerDuty or another incident manager? Do you check a Slack channel for alerts messages? Depending on the
answers to the previous questions, you'll have to make sure the orchestrator can be configured to alert to these third party apps.
Slack has a pretty good API so it should not be a problem to roll out something yourself, but since this is production alerting, it pays
to go with an out of the box solution that has been battle tested by many a ML team before you. 
