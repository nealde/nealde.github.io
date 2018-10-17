---
title: SPM Model Teardown
permalink: /docs/spm/
redirect_from: /docs/index.html
---

The Single Particle Model (SPM) is a relatively sophisticated physics-based lithium ion battery model.  It incorporates Fick's law of diffusion and Butler-Volmer kinetics, but does not include any electrolyte effects.

In practical terms, during charge, a lithium ion deintercalates from the cathode and, upon leaving the surface of the positive electrode particle, is instantly teleported to the surface of the negative electrode particle, where it intercalates and is stored internally.  The concentrations in both the positive and negative electrodes are tracked and the surface area is calculated based upon the assumed particle radius, electrode thickness, and active area per unit volume.  The particles are all assumed to be perfect spheres of uniform size.



<!-- Spotholder for an eventual walkthrough of the P2D model that I'll be doing, hopefully with annotated latex like this: $$y = x^2 \hbox{ when $x > 2$}$$

$$v_{rxn} = v_{mt}=\frac{i}{nFA}$$

# it's still not working

what if I put more markdown later in the sheet?

$$q = EC_d(1-e^{(-t/(R_sC_d))})$$

And then put another question later? -->
