# Scaling our demo app

- Our ultimate goal is to get more DockerCoins

  (i.e. increase the number of loops per second shown on the web UI)

- Let's look at the architecture again:

  ![DockerCoins architecture](images/dockercoins-diagram.svg)

- The loop is done in the worker;
  perhaps we could try adding more workers?

---

## Adding another worker

- All we have to do is scale the `worker` Deployment

.exercise[

- Open two new terminals to check what's going on with pods and deployments:
  ```bash
  kubectl get pods -w
  kubectl get deployments -w
  ```

<!--
```wait RESTARTS```
```keys ^C```
```wait AVAILABLE```
```keys ^C```
-->

- Now, create more `worker` replicas:
  ```bash
  kubectl scale deployment worker --replicas=2
  ```

]

After a few seconds, the graph in the web UI should ramp up.

---

## Adding more workers

- If 2 workers give us 2x speed, what about 3 workers?

.exercise[

- Scale the `worker` Deployment further:
  ```bash
  kubectl scale deployment worker --replicas=3
  ```

]

The graph in the web UI should go up again.

(This is looking great! We're gonna be RICH!)

---

## Adding even more workers

- Let's see if 10 workers give us 10x speed!

.exercise[

- Scale the `worker` Deployment to a bigger number:
  ```bash
  kubectl scale deployment worker --replicas=10
  ```

]

--

The graph will peak at 10 hashes/second.

(We can add as many workers as we want: we will never go past 10 hashes/second.)

---

class: extra-details

## Didn't we briefly exceed 10 hashes/second?

- It may *look like it*, because the web UI shows instant speed

- The instant speed can briefly exceed 10 hashes/second

- The average speed cannot

- The instant speed can be biased because of how it's computed

---

class: extra-details

## Why instant speed is misleading

- The instant speed is computed client-side by the web UI

- The web UI checks the hash counter once per second
  <br/>
  (and does a classic (h2-h1)/(t2-t1) speed computation)

- The counter is updated once per second by the workers

- These timings are not exact
  <br/>
  (e.g. the web UI check interval is client-side JavaScript)

- Sometimes, between two web UI counter measurements,
  <br/>
  the workers are able to update the counter *twice*

- During that cycle, the instant speed will appear to be much bigger
  <br/>
  (but it will be compensated by lower instant speed before and after)

---

## Why are we stuck at 10 hashes per second?

- If this was high-quality, production code, we would have instrumentation

  (Datadog, Honeycomb, New Relic, statsd, Sumologic, ...)

- It's not!

- Perhaps we could benchmark our web services?

  (with tools like `ab`, or even simpler, `httping`)

---

## Benchmarking our web services

- We want to check `hasher` and `rng`

- We are going to use `httping`

- It's just like `ping`, but using HTTP `GET` requests

  (it measures how long it takes to perform one `GET` request)

- It's used like this:
  ```
  httping [-c count] http://host:port/path
  ```

]
---

## Obtaining ClusterIP addresses

- We can simply check the output of `kubectl get services`

- Or do it programmatically, as in the example below

.exercise[

- Retrieve the IP addresses:
  ```bash
  kubectl get svc hasher -o go-template={{.spec.clusterIP}}
  kubectl get svc rng -o go-template={{.spec.clusterIP}}
  ```
]

---
## Getting a shell on our container network

We could use port forwarding technique again, but that would be cumbersome for 
interactive troubleshooting.  Let's do something different.

.exercise[
0. Create a shell using these instructions: 
   https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/
0. Log into the shell
0. Install httping

```bash
apt-get update
apt-get install httping
```
]
---

## Checking `hasher` and `rng` response times

.exercise[

- Check the response times for both services:
  ```bash
  httping -c 3 <HASHER_IP>
  httping -c 3 <RNG_IP>
  ```

]

- `hasher` is fine (it should take a few milliseconds to reply)

- `rng` is not (it should take about 700 milliseconds if there are 10 workers)

- Something is wrong with `rng`, but ... what?
