+++
title = "Nightmare on Kube street"
description = "If you stare at Kubernetes long enough, Kubernetes starts staring back."
date = 2026-10-23
authors = ["Aksum"]

[taxonomies]
tags = ["kubernetes", "yaml", "devops", "containers", "platform-engineering", "sheep"]
+++

I have been staring at the terminal for so long that when I blink, I can see indentation errors burned into the back of my eyelids. At some point in the last few weeks, I crossed a threshold and entered the Kubernetes K-hole: that special state of mind where reality stops arriving in three dimensions and starts presenting itself as a nested list of resources, selectors, tolerations, and quietly failing probes.

Last night I did not sleep. I deployed.

The dreamscape on the far side of `kubectl` is not a healthy place. It shimmers. It hums. It is made almost entirely of YAML and misplaced confidence. The clouds are high-availability clusters. The horizon is an ingress rule with no controller behind it. And the sheep? The sheep are gone. In their place roam electric Pods, glowing faintly blue, skittering between fences made of `NetworkPolicy` and mutual suspicion.

## The manifest destiny

In the dream, I was no longer a person. I was a manifest file.

Not a noble one, either. Not some elegant little ConfigMap with a simple life and modest ambitions. No, I was the pod template inside a deeply self-conscious Deployment, with a vague sense of purpose, a fragile emotional core, and resource requests just high enough to make scheduling uncomfortable.

I drifted through a digital pasture, `Pending`, waiting for the Scheduler to find me a Node. It came back every few seconds with the same verdict: `0/3 nodes are available: 3 Insufficient cpu.`

Above me, a hooded figure floated through the fog: the Controller Manager, tireless and unsmiling, trying over and over to reconcile my soul with my current state. Every time I thought I understood myself, it compared me against desired state and found me lacking.

The namespace was `default`, of course. It is always `default` in the bad dreams. The grass was made of green checkmarks. The ground underneath felt unstable, like an `emptyDir` trying to convince everyone it was a long-term storage strategy.

## Are you my node?

I remember an awful existential panic settling over me.

What if I don't belong to a Deployment at all? What if I belong to a StatefulSet and no one has told me? What if my identity depends entirely on volume claims and stable hostnames and I have been living a lie under a replica count of three?

If a container hangs in a cluster and there is no liveness probe to hear it, it stays `Running` forever. Green. Healthy. Dead inside.

I approached one of the electric Pods. It turned and looked at me with eyes made of blinking status LEDs and the quiet cruelty of machine certainty.

"Your indentation is off by two spaces in the heart, traveler," it said.
"You will never reach Ready like this."

I wept, but my tears were log streams, piped neatly into a void where `stdout` goes to die and no one ever thinks to look.

## The great garbage collection

Then the Horizontal Pod Autoscaler arrived, and the dream changed shape.

At first there were two of me. Then six. Then twenty.

A whole chorus line of identical selves, all stamped from the same template, all carrying the same image tag, all convinced we were special. We danced a frantic and deeply undignified ballet of `CrashLoopBackOff`, collapsing in perfect synchronization and staggering back up, each pause twice as long as the last: ten seconds, twenty, forty, until we lay on the floor for five whole minutes between attempts. Every one of us marked correctly, labeled beautifully, and absolutely nowhere useful to send traffic.

It was a tragedy of immaculate metadata.

Then the word came down: `Terminating`.

You never forget the first time you feel your grace period start. The world narrows. The lights dim. The `preStop` hook fires first, a panicked farewell to a database you never truly knew, only circled warily from across the service mesh like two animals at the edge of a poisoned lake. Then `SIGTERM`. Then whatever is left of thirty seconds, and you feel every one of them go. Then `SIGKILL`, which does not negotiate.

One by one, the replicas vanished. The cluster exhaled. We scaled down to one and no further, because the HPA will not go to zero. Someone always has to stay behind.

And there, in that thinning silence, the truth arrived like a `kubectl describe` at 3:17 in the morning:

*We are all ephemeral containers in the great cluster of life*.

## Back to reality

I woke up sweating, one hand reaching for a coffee mug that I was briefly disappointed to discover had no `containerPort` assigned to it.

For a few disorienting seconds, I expected the bedroom to have labels. I half-believed the cat might be tainted. I looked at the window and felt genuine relief that the cloud outside was the ancient, analog kind with no control plane, no YAML, and no opinions about desired state.

That is probably the warning sign.

If you find yourself dreaming in manifests, if your inner monologue starts sounding like `kubectl get events`, if you begin to regard ordinary household objects as resources awaiting reconciliation, it may be time to step away from the abstraction before the K-hole grows teeth. Take a break. Go outside. Look at a real cloud.

The kind that doesn't have an **API**.
