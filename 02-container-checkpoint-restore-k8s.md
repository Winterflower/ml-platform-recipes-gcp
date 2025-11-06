# Compute Resiliency - aka what I'd like to see from the Container Checkpoint Restore Feature in k8s

One of the conundrums of building a reliable ML platform that is meant to be used by folks who have deep knowledge
of how to build models, but maybe lack knowledge of computer science fundamentals is how to allocate compute. 

The problem really is that most modern compute platforms force your hand into defining some sort of numbers
for the resources that your task/job/workload would like to request. 

For example, if you are running you machine learning pipelines on Kubernetes and say each task in your machine
learning pipeline runs as an individual container inside a pod, then you might be familiar with the fact that in every kubernetes
pod deployment, you generally specify some memory and cpu values that your pod needs to request. 

When we are dealing with machine learning training workloads, estimating this without having trial runs
can be tricky. Well, one can say, you can look at the number of physical bytes your training dataset
etc requires in storage or the logical bytes and make some smart guesstimates based on that, but it's not that simple. 

Here is a concrete real life example that I was dealing with - a Big Query table that was 25 GB in logical bytes
was being read into a container running on a k8s node. The node had 60GB of memory, but the task kept failing
with an OOM. When profiling, it turned out that temporarily during the creation of a dataframe from this table, the
memory usage spiked to 70GB. 

Similar things can happen during model training or ingestion or really any long-running process.
The more long-running the process, the more annoying it will be that you will lose all of the progress
you've made up until that point at hour 10 and have to start over again. 

Many modern machine learning and deep learning frameworks like PyTorch offer various model
checkpointing functionality, but what if you just want to make sure that any workload you run (whether it's just reading
some data from data warehouse) can restart in the even of resource exhaustion or another node failure or pre-emption. 

Recently, I learned by reading blogposts from a machine learning infrastructure company called
Modal that there exists functionality for live migration of containers developed and maintained
by a project called CRIU (checkpoint restore in userspace). I also learned that the first part of the
functionality (ie. the ability to take a checkpoint of a running container) had already been
implemented and merged into Kubernetes. 

Here are some high level design decisions that would make sense for a fully-featured container checkpoint/restore functionality in
k8s from a machine learning engineer's point of view:

- a container is configured as checkpoint-restore eligible in the deployment manifest
- 
