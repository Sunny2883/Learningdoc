# Learningdoc
Managing Dependencies in Terragrunt with Mock Outputs
Issue: When using Terragrunt with modules that have dependencies, destroying infrastructure can fail due to the dependency chain.

Solution: Implement mock outputs in the dependency configuration.

What Mock Outputs Do:

Mock outputs allow you to define placeholder values for outputs from dependent modules.
They enable you to bypass the need for actual output values during operations like destruction.
Benefits:

By using mock outputs, you can prevent dependency-related issues when running the terragrunt destroy-all command.
This approach simplifies the destruction process by avoiding conflicts caused by the interdependencies of resources.


=================================================================================================================

ECS Resource Requirements for Task Deployment
Issue: While deploying a .NET application on AWS ECS with EC2, the task failed to start.

Reason: AWS ECS requires a minimum of:

0.25 CPU units (256 CPU shares)
0.5 GiB (512 MiB) of memory
Implication:

Values below these limits, such as 0.1 CPU units and 0.1 GiB of memory, are considered insufficient and may result in task failure or rejection.

================================================================================================================


Zero Downtime Deployment on AWS ECS
Issue: During the deployment of an application on AWS ECS with EC2, zero downtime deployment was not achieved due to insufficient memory.

Solution: Adjusted the deployment_configuration settings:

minimum_healthy_percent: Specifies the lower limit on the number of healthy tasks during a deployment.
maximum_percent: Specifies the upper limit on the number of tasks that can run during a deployment.
Result: Making these adjustments enabled successful zero downtime deployment.
