---
title: Battery.py - A Framework for Performance Battery Models
permalink: /docs/battery/
---

### The Sci-Kit Learn of Battery Simulation

Battery simulation is hard. The equations are typically very stiff, and often take a large number of nodes to become self-convergent, in particular at high discharge rates.  The goal of [Battery.py](http://github.com/nealde/battery) is to make this a little easier, to let experimentalists worry about what experiments they want to run and let the models take care of themselves.

The syntax for using the models is very simple, and features a sci-kit learn-like interface:
```python
from battery import SingleParticle
spm = SingleParticle()
```

The model is then interacted with as a model, with certain methods like **charge**:

![charge]({{ site.baseurl }}/img/battery/charge.png)

In addition to simulation of batteries, one of the main draws is how easy it makes the process of model calibration:

```python
spm.fit([t_exp], [v_exp], currents=[0.5], maxiter=1000, tol=1e-7)
```

This results in the following:
![fit]({{ site.baseurl }}/img/battery/initial_to_fit.png)

In addition to simple charges and discharges, the model is able to string together arbitrary combinations of charging and discharging, like so:

```python
pwc = spm.piecewise_current([500,400,600,200,50,700,300,2000], [1.0,-0.5,1.2,-0.75,2.0,0.5,-1.0,1.5])
```

Which results in the following curve:
![piecewise]({{ site.baseurl }}/img/battery/piecewise.png)

This can also be used for fitting.

Although only the Single Particle Model is supported now, more sophisticated models will be implemented in the future, so stay tuned!
