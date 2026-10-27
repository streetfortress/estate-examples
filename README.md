# Forts for Software Architects

Because forts provides robust semantics around the machine and workload lifecycle, it's easy to experiment with different architectural patterns:

- Each fort is a self contained k3s instance. Ready to run web services, cache, object storage, and more.
- An estate mesh supports hybrid and multi-cloud scenarios. Every fort gets an addressable name no matter if it's on prem, in the cloud, or spread out across multiple geographic regions.
- An estate itself is a git repo, so you can A/B test different branches and configurations easily. The exact deployment history is recorded in git for posterity.

These properties lend themselves well to the software architect. Imagine:

- An on-prem data lake with geo-distributed web/cache endpoints
- Dynamic dev and staging environments for humans and agent sandboxes
- Uniform configuration for observability engines and incident response

## Experiment with Databases,  Caches, Queues, Durable Objects, and More

With a fort, it's "just nixOS and kubernetes", so you really can do just about anything with one:
- Databases: Postgres via **cloud native postgres**
- Cache: Redis-like via **valkey**
- Queues: Postgres gets you surprisingly far, or choose your favorite offering
... You get the idea. Any off-the-shelf component is at your fingertips.

Forts provides **a strong operational posture** out of the box - so your experiments here are fit for task when you're ready to promote a change. 

