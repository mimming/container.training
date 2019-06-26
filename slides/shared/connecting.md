class: in-person

## Doing or re-doing the workshop on your own?

- This workshop assumes no weird security stuff that might exist in some GCP orgs

- Use something like
  [Play-With-Docker](http://play-with-docker.com/) or
  [Play-With-Kubernetes](https://training.play-with-kubernetes.com/)

  Zero setup effort; but environment are short-lived and
  might have limited resources

- Create your own cluster (local or cloud VMs)

  Small setup effort; small cost; flexible environments

- Create a bunch of clusters for you and your friends
    ([instructions](https://@@GITREPO@@/tree/master/prepare-vms))

  Bigger setup effort; ideal for group training

---

class: self-paced

## Get your own Docker nodes

- If you already have some Docker nodes: great!

- If not: let's get some thanks to Play-With-Docker

.exercise[

- Go to http://www.play-with-docker.com/

- Log in

- Create your first node

<!-- ```open http://www.play-with-docker.com/``` -->

]

You will need a Docker ID to use Play-With-Docker.

(Creating a Docker ID is free.)

---

## Terminals

Once in a while, the instructions will say:
<br/>"Open a new terminal on node1."

This is because I haven't gone back and updated everything to refer to Cloud Shell explicitly

Just open a new tab in Cloud shell


---

## Tmux cheatsheet

[Tmux](https://en.wikipedia.org/wiki/Tmux) is a terminal multiplexer like `screen`.

*You don't have to use it or even know about it to follow along.
<br/>
But some of us like to use it to switch between terminals.
<br/>
It has been preinstalled on your workshop nodes.*

- Ctrl-b c → creates a new window
- Ctrl-b n → go to next window
- Ctrl-b p → go to previous window
- Ctrl-b " → split window top/bottom
- Ctrl-b % → split window left/right
- Ctrl-b Alt-1 → rearrange windows in columns
- Ctrl-b Alt-2 → rearrange windows in rows
- Ctrl-b arrows → navigate to other windows
- Ctrl-b d → detach session
- tmux attach → reattach to session

---

## Create a GKE cluster
We need a Kubernetes cluster, so lets make one the easy way (with GKE).  This takes awhile, so we'll let it run in the 
background. 

.exercise[
0. Go to Kubernetes Engine on the GCP Console
0. Wait for the API to be enabled
0. Select 'Standard' cluster
0. Change the node pool from 3 to 5
0. Click 'Create'
0. Wait -- or better yet do GKE cluster creation stretches
]
