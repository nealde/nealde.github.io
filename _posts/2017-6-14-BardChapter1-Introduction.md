---
layout: post
title: Chapter 1 - Introduction
mathjax: true
---

### Requirements 
##### Necessary:
* ipywidgets (conda install ipywidgets)
* bokeh (conda install bokeh)
* numpy (installed by default)
##### Optional:
* [Codefolding](https://github.com/ipython-contrib/jupyter_contrib_nbextensions/wiki/Codefolding)
- Install by typing the following into Anaconda prompt:
* pip install jupyter_contrib_nbextensions
* jupyter contrib nbextension install --user
* jupyter nbextension enable codefolding/main
* Restart Jupyter and in the /Tree directory, check for an NbExtensions tab and see that Codefolding is checked

### Table of Contents:
  * [Chapter 1.2](#chapter-2) Double Layer Capacitance
  * [Chapter 1.3](#chapter-3) Fardaic Processes
  * [Chapter 1.4](#chapter-4) Modes of Mass Transfer
  * [Chapter 1.5](#chapter-5) Nernstian Reactions with Coupled Chemical Reactions
  

### Chapter 1.2 <a id="chapter-2"></a>Double Layer Capacitance
![Figure 1.2.3]({{ site.baseurl }}/images/CH1/fig_1_2_3.png)
##### Some Equations:
**Voltage Step**   
Response current: $$ i = \frac{E}{R_s}e^{(-t/(R_sC_d))} $$
Charge stored: $$ q = EC_d(1-e^{(-t/(R_sC_d))}) $$


### Equivalent Linear Circuits
![Figure 1.2.5]({{ site.baseurl }}/images/CH1/fig_1_2_5.png)


```python
# Optional check to install / make sure CodeFolding is active:

# Activate:
# ext_require_path = 'codefolding/main'
# try:  # notebook >= 4.2.0
#     from notebook.nbextensions import enable_nbextension
#     enable_nbextension('notebook', ext_require_path)
# except ImportError:
#     from notebook.nbextensions import EnableNBExtensionApp
#     EnableNBExtensionApp().enable_nbextension(ext_require_path)

# Check:
# from notebook.nbextensions import check_nbextension
# check_nbextension('codefolding', user=True)
# check_nbextension('codefolding/main.js', user=True)
```


```python
# Create the interactive graphs
from ipywidgets import interact
import numpy as np

from bokeh.io import push_notebook, show, output_notebook
from bokeh.layouts import gridplot
from bokeh.plotting import figure
output_notebook()

x = np.linspace(0, 60, 200)
E = 0.5
y = x*E*1/x
# Rs in Ohms
# Cd in uF
Rs=1
Cd=20
y2 = E/Rs*np.exp(-x/(Rs*Cd))

p = figure(title="Applied E", plot_height=300, plot_width=600, y_range=(-3,3))
pp = figure(title="Resultant i", plot_height=300, plot_width=600, x_range = p.x_range, y_range=p.y_range)
r = p.line(x, y, color="#2222aa", line_width=3)
rr = pp.line(x, y2, color="#2222aa", line_width=3)

def update(f, E=0.1, Rs=1, Cd=20):
    if   f == "step": 
        r.data_source.data['y'] = x*E*1/x
        rr.data_source.data['y'] = E/Rs*np.exp(-x/(Rs*Cd))
    elif f == "ramp":
        r.data_source.data['y'] = (E*x)/max(x)
        v=E/max(x)
        rr.data_source.data['y'] = v*Cd*(1-np.exp(-x/(Rs*Cd)))
    elif f == "triangle":
        r.data_source.data['y'] = np.concatenate((np.array((2*E*x)/max(x))[0:len(x)/2-1],np.array(2*E-(2*E*x)/max(x))[len(x)/2:len(x)-1]),axis=0)
        v=E/max(x)
        rr.data_source.data['y'] = np.concatenate((np.array(v*Cd*(1-np.exp(-x/(Rs*Cd))))[0:len(x)/2-1],np.array(-v*Cd*(1-np.exp(-x/(Rs*Cd))))[0:len(x)/2-1]),axis=0)
    push_notebook()
    
s = gridplot([[p],[pp]], toolbar_location=None)
show(s, notebook_handle=True)

```

```python
# Create the sliders for the graphs
interact(update, f=["step", "ramp", "triangle"], E=(0,3,0.05), Rs=(0.01,3,0.01), Cd=(0.01, 60, 0.1))
```
<iframe src="my_plot.html">

</imframe>



### Chapter 1.3 <a id="chapter-3"></a>Faradaic Processes
##### Galvanic cells - Reactions occur spontaneously at the electrodes when connected by a conductor.
* Primary and secondary batteries
* Fuel cells

##### Electrolytic cells - Reactions are effected by the applied voltage moreso than the open Circuit Potential (OCP)
* Electrolytic synthesis
* Electroplating
* Electrorefining (like copper)

The function of a half-cell is independent of galvanic or eletrolytic status.  
**Reduction** occurs at the cathode, **oxidation** occurs at the anode.

The equilibrium potential is an important reference point of the system.  
* Cell potential changing from equilibrium is called *polarization*
* Extent of polarization is measured by overpotential \\(\eta = E-E_{eq})\\
![Figure 1.3.5]({{ site.baseurl }}/images/CH1/fig_1_3_5.png)  

##### Factors affecting electrode reaction rate and current
1. Mass transfer
2. Electron transfer at electrode surface
3. Chemical reactions before / after electron transfer
4. Other surface reactions, like adsorption, desorption, or crystallization  
*rate constants may be dependent upon potential*  
**Overpotential** is the sum of terms associated with each step - *mass transfer overpotential*, *charge-transfer overpotential*, etc  
$$E_{applied} = E_{cd}-iR_s=E_{eq,cd}+\eta-iR_s$$  
The above indicates that the *overpotential* and *bulk soluion resistance* should be counted separately
![Figure 1.3.6]({{ site.baseurl }}/images/CH1/fig_1_3_6.png)  
##### 3-electrode cells for measuring electrochemical cell resistance
![Figure 1.3.10]({{ site.baseurl }}/images/CH1/fig_1_3_10.png)  
![Figure 1.3.12]({{ site.baseurl }}/images/CH1/fig_1_3_12.png)  

### Chapter 1.4 <a id="chapter-4"></a>Modes of Mass Transfer
If an eelctrode process has fast, heterogeneous charge-transfer kinetics and mobile, reversible homogeneous equations:
1. The homogeneous reactions can be regarded as being at equilibrium
2. the *surface concentrations* are related to the electrode potential by an equation of the Nernst form, meaning that the reaction is sufficiently fast as to be mass-transfer limited. $$v_{rxn} = v_{mt}=\frac{i}{nFA}$$  

##### 3 modes of mass transfer:
1. Migration - Movement of a charged body under the influence of an electric field (a gradient of electrical potential).
2. Diffusion - Movement of a species under the influence of a gradient of chemical potential (i.e. a concentration gradient).
3. Convection - Stirring or hydrodynamic transport. Generally, fluid flow occurs because of natual convection (convection caused by density gradients) and forced convection, and may be characterized by stagnant regions, laminar flow, and turbulent flow.  

Mass transfer to an electrode is governed by the *Nernst-Planck equation (1D)*
$$J_{i}(x) = -D_{i}\frac{\partial C_i(x) }{\partial x}-\frac{z_iF}{RT}D_iC_i\frac{\partial \phi (x) }{\partial x} +C_iv(x)$$  

The transport of C to the electrode is mass-transfer limited, such that the diffusion layer thickness is always constant as long as D is constant.
![Figure 1.4.1]({{ site.baseurl }}/images/CH1/fig_1_4_1.png)  
$$\frac{i}{nFA}=m_O[C^*_O-C_O(x=0)]$$ $$i_l=nFAm_OC^*_O$$  

**When R is not present:** $$E = E_{1/2} = E^{0'}-\frac{RT}{nF}ln\frac{m_O}{m_R}$$  
$$E = E_{1/2} +\frac{RT}{nF}ln\frac{i_l-i}{i}$$ 

**When R present:** $$E = E^{0'}-\frac{RT}{nF}ln\frac{m_O}{m_R}+\frac{RT}{nF}ln(\frac{i_{l,c}-i}{i-i_{l,a}})$$  

**When R is insoluble (like with metal plating)** $$E = E^{0'}-\frac{RT}{nF}lnC^*_O+\frac{RT}{nF}ln(\frac{i_l-i}{i})$$  
$$i = i_l, \eta_{conc} \rightarrow \infty$$
![Figure 1.4.4]({{ site.baseurl }}/images/CH1/fig_1_4_4.png)  


### Chapter 1.5 <a id="chapter-5"></a>Nernstian Reactions with Coupled Chemical Reactions
$$E = E^{0'} + \frac{RT}{nF}ln\frac{m_R+\mu k}{m_O}+\frac{RT}{nF}ln(\frac{i_l-i}{i})$$  

$$E = E_{1/2}' + \frac{0.059}{n}ln\frac{m_R+\mu k}{m_O}$$  

$$m_R=0.62D_R^{2/3}v^{-1/6}\omega ^{1/2}$$
##### Unperturbed equation:
$$E'_{1/2} = E_{1/2} + \frac{0.059}{n}ln\frac{\mu k}{m_R}$$  

##### Rotating equation:
$$E'_{1/2} = E_{1/2} + \frac{0.059}{n}ln\frac{\mu k}{0.62D_R^{2/3}v^{-1/6}} - \frac{0.059}{n}ln \omega$$  
![Figure 1.5.1]({{ site.baseurl }}/images/CH1/fig_1_5_1.png)  


```python
# Create the interactive graphs
from ipywidgets import interact
import numpy as np

from bokeh.io import push_notebook, show, output_notebook
from bokeh.layouts import gridplot
from bokeh.plotting import figure
output_notebook()
# Needs E1/2, DR, v, n, u, k, w, il, mR as inputs.
x = np.linspace(0, 60, 200) # i values
E_half = 0.5
il=60
n=0.01
u=.01
k=1
mR=0.5
DR=0.02
v=0.3
w=3

E_half_1 = E_half+0.059/n*np.log(u*k/mR)
E_half_2 = E_half+0.059/n*np.log(u*k/(0.62*DR**(2/3)*v**(-1/6)))-0.059/n*np.log(w)
w=6
E_half_3 = E_half+0.059/n*np.log(u*k/(0.62*DR**(2/3)*v**(-1/6)))-0.059/n*np.log(w)
y = E_half_1 + 0.059/n*np.log((il-x)/x) # E values
y2 = E_half_2 + 0.059/n*np.log((il-x)/x)
y3 = E_half_3 + 0.059/n*np.log((il-x)/x)

p = figure(title="E vs i for Spinning Disc Electrode", plot_height=300, plot_width=600, y_range=(-80,10))
# pp = figure(title="Resultant i", plot_height=300, plot_width=600, x_range = p.x_range, y_range=p.y_range)
r = p.line(x, y, color="#2222aa", line_width=3)
rr = p.line(x, y2, color="red", line_width=3)
rrr = p.line(x, y3, color="green", line_width=3)
# rr = pp.line(x, y2, color="#2222aa", line_width=3)

def update(E_half=0.5, il=60, n=0.01, u=0.01, k=1, mR=0.5, DR=0.02, v=0.03, w=3):
    E_half_1 = E_half+0.059/n*np.log(u*k/mR)
    E_half_2 = E_half+0.059/n*np.log(u*k/(0.62*DR**(2/3)*v**(-1/6)))-0.059/n*np.log(w)
    w=2*w
    E_half_3 = E_half+0.059/n*np.log(u*k/(0.62*DR**(2/3)*v**(-1/6)))-0.059/n*np.log(w)
    r.data_source.data['y'] = E_half_1 + 0.059/n*np.log((il-x)/x)
    rr.data_source.data['y'] = E_half_2 + 0.059/n*np.log((il-x)/x)
    rrr.data_source.data['y'] = E_half_3 + 0.059/n*np.log((il-x)/x)
    push_notebook()
    
s = gridplot([[p]], toolbar_location=None)
show(s, notebook_handle=True)

```
```python
# Create the sliders for the graphs
interact(update,  E_half=(0,3,0.05), il=(10,60,1), n=(0.01, .1, 0.01), u=(0.01, 1, 0.01),
        k=(0.05, 2, 0.05), mR=(0.3, 1, 0.1), DR=(0.01, .8, 0.01), v=(0.01, .8, 0.01),
        w=(0.0, 60, 1))
```
```python

```
