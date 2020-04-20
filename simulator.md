# Methods for simulating FCM's.
<div align = justify>
When creating an FCM, the emphasis is on fuzzy logic as shown in the earlier section on FCMs. However, when using an FCM, the emphasis shifts to mathematics that are similar to neural networks. Indeed, an FCM can also be seen as a “recurrent artificial neural network that creates models as collections of concepts/neurons and the various causal relations that exist between them” (Tsadiras, 2008). Inferring how a determinant quantitatively impacts another is thus analogous to computing how the firing of a neuron propagates throughout a network (Dickerson & Kosko, 1994). Consequently, the equation governing the update of an FCM is often known as ‘activation function’, using the same vocabulary as in neural networks.

In the current version, the fcmbci module implements three types of activation functions; Kosko's, modified Kosko's and Rescaled function. 
</div>

Kosko (Stach et al., 2010):
<div class=container, align=center>

![{d}_i^{s+1}=f(\sum_{j=1} \mathbf{d}_j^s * \mathbf{C}_{ij})](https://render.githubusercontent.com/render/math?math=%7Bd%7D_i%5E%7Bs%2B1%7D%3Df(%5Csum_%7Bj%3D1%7D%20%5Cmathbf%7Bd%7D_j%5Es%20*%20%5Cmathbf%7BC%7D_%7Bij%7D))

</div>

Modified Kosko (Papageorgiou, 2011):
<div class=container, align=center>

![\mathbf{d}_i^{s+1}= f(\mathbf{d}_i+\sum_{j=1} \mathbf{d}_j^s * \mathbf{C}_{ij})\\](https://render.githubusercontent.com/render/math?math=%5Cmathbf%7Bd%7D_i%5E%7Bs%2B1%7D%3D%20f(%5Cmathbf%7Bd%7D_i%2B%5Csum_%7Bj%3D1%7D%20%5Cmathbf%7Bd%7D_j%5Es%20*%20%5Cmathbf%7BC%7D_%7Bij%7D)%5C%5C)

</div>

Rescaled (Papageorgiou, 2011):
<div class=container, align=center>

![\mathbf{d}_i^{s+1} = f((2\mathbf{d}_i -1)  +\sum_{j=1} (2\mathbf{d}_j^s -1) * \mathbf{C}_{ij})\\](https://render.githubusercontent.com/render/math?math=%5Cmathbf%7Bd%7D_i%5E%7Bs%2B1%7D%20%3D%20f((2%5Cmathbf%7Bd%7D_i%20-1)%20%20%2B%5Csum_%7Bj%3D1%7D%20(2%5Cmathbf%7Bd%7D_j%5Es%20-1)%20*%20%5Cmathbf%7BC%7D_%7Bij%7D)%5C%5C)

</div>

<div align = justify>

Note that a (transfer) function f is applied to the result. As shown in equations above, this function is necessary to keep values within the range [-1, 1]. In the current version, four such functions are implemented:

* Sigmoid:

<div class=container, align=center>

![f(x)=\frac{1}{1+e^{-\lambda x}}, x\in\mathbb{R}; \[0,1\]](https://render.githubusercontent.com/render/math?math=f(x)%3D%5Cfrac%7B1%7D%7B1%2Be%5E%7B-%5Clambda%20x%7D%7D%2C%20x%5Cin%5Cmathbb%7BR%7D%3B%20%5B0%2C1%5D)

</div>

* Hyperbolic Tangent:

<div class=container, align=center>

![f(x)=tanh(x)=\frac{sinh(x)}{cosh(x)}=\frac{e^{2x}-1}{e^{2x}+1}, x\in\mathbb{R}; \[-1,1\]\\](https://render.githubusercontent.com/render/math?math=f(x)%3Dtanh(x)%3D%5Cfrac%7Bsinh(x)%7D%7Bcosh(x)%7D%3D%5Cfrac%7Be%5E%7B2x%7D-1%7D%7Be%5E%7B2x%7D%2B1%7D%2C%20x%5Cin%5Cmathbb%7BR%7D%3B%20%5B-1%2C1%5D%5C%5C)

</div>

* Bivalent:

<div class=container, align=center>

![f(x)=\begin{Bmatrix}  1, & x > 0\\   0, & x\leq 0 \end{Bmatrix}](https://render.githubusercontent.com/render/math?math=f(x)%3D%5Cbegin%7BBmatrix%7D%20%201%2C%20%26%20x%20%3E%200%5C%5C%20%20%200%2C%20%26%20x%5Cleq%200%20%5Cend%7BBmatrix%7D)

</div>

* Trivalent:

<div class=container, align=center>

![f(x)=\begin{Bmatrix} 1, & x > 0 \\  0, & x= 0\\  -1, & x < 0 \\ \end{Bmatrix}](https://render.githubusercontent.com/render/math?math=f(x)%3D%5Cbegin%7BBmatrix%7D%201%2C%20%26%20x%20%3E%200%20%5C%5C%20%200%2C%20%26%20x%3D%200%5C%5C%20%20-1%2C%20%26%20x%20%3C%200%20%5C%5C%20%5Cend%7BBmatrix%7D)

</div>

<div align = justify>

The FcmSimulator module includes methods for runing simulations on an FCM and testing <em>what-if</em> scenarios. In the current version, it includes two methods: [simulate()](#simulate) and [test_scenario()](#test_scenario). You can create an FcmSimulator instance by either supplying an initial state vector and a weight matrix or leaving it empty.

Example:

```
sim = FcmSimulator(init, weight)
```
In the first case, the module automatically runs an FCM simulation on the supplied initial state vector and finds the equilibrium state and stores it in the constructor to be used for testing of scenarios.

If one is not interested in testing scenarios, one could leave it empty.

```
sim = FcmSimulator()
```

## simulate()

The simulate method allows one to run simulations on the defined FCM. The method takes two parameters state and weights. The state is the initial state vector of the concepts in the FCM and the weights is the matrix with the edge weights. The optional arguments include the iteration steps, inference method, transfer function, lambda parameter and the threshold parameter.  

```
simulate(state, weights, iterations = 50, inference = 'mk', 
                 transfer = 's', l = 1, thresh = 0.001)

Parameters
----------
State : dict,
            A dictionary of Concepts as keys and their states. ---> {'C1': 0.5, 'C2' : 0.4}.
            The states take only values in the range of [0,1] for the sigmoid transfer function and [-1,1] for the hperbolic tangent.

weights : Data frame with the causal weights.

iterations : int,
                Number of itterations to run in case if the system doesn't converge.
inference : str,
            default --> 'mk' -> modified kosko; available options: 'k' -> Kosko, 'r' -> Rescale.
            Method of inference.
                    
transfer : str,
            default --> 's' -> sigmoid; available options: 'h' -> hyperbolic tangent; 'b' -> bivalent; 't' trivalent. 
            transfer function.
l : int,
    A parameter that determines the steepness of the sigmoid and hyperbolic tangent function at values around 0. 
        
thresh : float,
            default -->  0.001,
            a thershold for convergence of the values.

Return
----------
y : dataframe,
    dataframe with the results of the simulation steps.

```

The simulation is itterated until either of the two conditions are met: 1) output (A) converges to a fixed point attractor (delta(T) <= 0.001); or 2) maximum number of itterations passed to the function is reached. The latter indicates that either a cyclic or a chaotic behavior of the system (Napoles et al., 2020).

Example:

In the example below, we first define the weight matrix, the state vector and then use the simulate method to run the simulation. The example is taken from the fcm package in r by Dikopoulou and Papageorgiou. 

```
C1 = [0.0, 0.0, 0.6, 0.9, 0.0, 0.0, 0.0, 0.8]
C2 = [0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 0.5]
C3 = [0.0, 0.7, 0.0, 0.0, 0.9, 0.0, 0.4, 0.1]
C4 = [0.4, 0.0, 0.0, 0.0, 0.0, 0.9, 0.0, 0.0]
C5 = [0.0, 0.0, 0.0, 0.0, 0.0, -0.9, 0.0, 0.3]
C6 = [-0.3, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]
C7 = [0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 0.4, 0.9]
C8 =[0.1, 0.0, 0.0, 0.0, 0.0, 0.1, 0.6, 0.0]

weights = pd.DataFrame([C1,C2, C3, C4, C5, C6, C7, C8], 
                    columns=['C1','C2','C3','C4','C5','C6','C7','C8'])

state = {'C1': 1, 'C2': 1, 'C3': 0, 'C4': 0, 'C5': 0,
                        'C6': 0, 'C7': 0, 'C8': 0}

sim = FcmSimulator()

res = sim.simulate(state, weights, inference = 'mk')

```

## test_scenario
The test_scenario() method allows runing FCM simulations of what-if scenarios by supplying/changing the state vectors. Changing the state vector implies turning on/off certain concepts in the system. Most of the default parameters are the same as in simulate method described above. The scenario name is the name of the scenario to be tested. The state vector is a dictionary with the keys of the concepts to be changed in the state vector that has been supplied in when creating an FcmSimulator instance.

```
test_scenario(scenario_name, state_vector, weights, 
                iterations = 50, inference = 'mk', transfer = 's', l = 1, thresh = 0.001):

Parameters
----------
scenario_name : str,
                name of the tested scenario.
state_vector : dict,
                dictionary of the concepts that needs to be turned on or off. 

weights : Data frame with the causal weights.

iterations : int,
                Number of itterations to run in case if the system doesn't converge.
inference : str,
            default --> 'mk' -> modified kosko; available options: 'k' -> Kosko, 'r' -> Rescale.
            Method of inference.
                    
transfer : str,
            default --> 's' -> sigmoid; available options: 'h' -> hyperbolic tangent; 'b' -> bivalent; 't' trivalent. 
            transfer function.
l : int,
    A parameter that determines the steepness of the sigmoid and hyperbolic tangent function at values around 0. 
        
thresh : float,
            default -->  0.001,
            a thershold for convergence of the values.
```

</div>

