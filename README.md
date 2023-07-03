# change-point-optative
Change Point Optative Project

- [Lázaro Daniel González Martínez](https://github.com/LazaroDGM)  
    [lazarodaniel.glezmtnz@gmail.com](mailto:lazarodaniel.glezmtnz@gmail.com)
- [Alejandra Monzón Peña](https://github.com/Alejandra1113)  
    [correo de ale](mailto:)
- [Leonardo Ulloa Ferrer](https://github.com/Leo00010011)  
    [correo del chino](mailto:)

# Question 1

Simulate a time series with $n = 500$ realisations distributed according to a probability law $L$ of your choice. This time series must have $K^∗ ≥ 2$ changes in one or more dimensions of the parameters of $L$ (you set the value of $K^∗$ as you wish). Plot this time series.

## Answer 1

We choose an Exponential distribution for the time series simulation, the code below shows the function `expovariate`, which create functions that generates random values of exponential distibution with parameter $\lambda = l$. 

Now let's generate a time serie on time interval $[0; 500]$, and let's add 3 change points on $200$, $250$ and $350$, for that we generate random exponentialy distributed values, using the previously defined functions exp_1, exp2 adn exp_3, getting $4$ intervals $[0; 200)$, $[200,250)$, $[250;350)$ and $[350,500]$ with values coresponding to exponential distributions with $\lambda = 1,5,1,4$ respectively using `generate_time_series` function. Finally we plot the resulting time series and the real change points moments with `plot_time_series` function.

![Time Series Example](img/example_time_series.png)

# Question 2

Derive a cost function that is appropriate for the data simulated in Question 1. Briefly justify your choice (in a comment in your code).

## Answer 2


Assuming that is known that the time series has exponential distribution, but the $\lambda$ of the distribution of each interval, is not. We are going to use the negative logarithm of likelihood function. Let's start finding those $\lambda$ values, for which the value of this function is minimal, keeping constant $y_i$ (value of time series at instant $i$) values for all  $0 < i \le n$. Note that find the minimum value, is equivalent to find the maximum in the possitive function, and that can be done finding the **maximum likelihood estimator**. Given $y_i$, we must maximice:

<!-- Supongamos que sabemos que los puntos simulados tienen distribución exponencial pero no sabemos con qué parámetro $\lambda$, para cada uno de los intervalos entre `CP` y `CP`. Para ello utilizaremos como función de costo el logaritmo negativo de la función de verosímilitud. Pero antes debemos hallar para qué valor de $\lambda$ este valor es mínimo manteniendo constante los valores de $y_i$ para todo $0 < i \le n$. Note que hallar el valor mínimo es equivalente a hallar el valor máximo de la misma función positiva, lo cual sería el método del máximo verosímil. Dada cada $y_i$, debemos maximizar: -->

$$ L(\lambda | y_1...y_n) = Π_{i=1}^n \lambda e^{-\lambda y_i}  = \lambda^n e^{-\lambda \sum_{i = 1}^{n} y_i} $$

Which is equivalent to maximice:
<!-- Sin embargo esto es equivalente a maximizar: -->

$$ \ln(\lambda^n e^{-\lambda \sum_{i = 1}^{n} y_i}) = \ln(\lambda^n) + \ln(e^{-\lambda \sum_{i = 1}^{n} y_i}) = n\ln(\lambda) -\lambda \sum_{i = 1}^{n} y_i $$

At last we derive and make the function equal $0$, in order to find such value:
<!-- Por último derivamos e igualamos a $0$ para encontrar este valor: -->

$$ 0 = \frac{dL(n\ln(\lambda) -\lambda \sum_{i = 1}^{n} y_i)}{d\lambda} = \frac{n}{\lambda} - \sum_{i = 1}^{n} y_i$$

Finally we get the estimated expression value of $\lambda$:
<!-- Y finalmente obtenemos que el valor estimado de $\lambda$ es: -->

$$ \lambda_{MLE} = \frac{n}{\sum_{i = 1}^{n} y_i}$$

With the estimated $\lambda$, we go back on our possitive cost function and replace $\lambda$ expression on it:
<!-- Ahora bien, estimado $\lambda$ regresamos a nuestra función de costo versión positiva y sustituimos obteniendo: -->

$$ n\log(\lambda_{MLE}) -\lambda_{MLE} \sum_{i = 1}^{n} y_i = n\log \left(\frac{n}{\sum_{i = 1}^{n} y_i} \right) - \frac{n}{\sum_{i = 1}^{n} y_i} \sum_{i = 1}^{n} y_i$$ 
$$ = n\log \left(\frac{n}{\sum_{i = 1}^{n} y_i} \right) - n = n \left(\log \left(\frac{n}{\sum_{i = 1}^{n} y_i} \right) - 1\right) $$

But, the purpose of this in the end is minimaze this value at the change points, so we need to work with the opposite, the negative of this function, the original cost function, so it is:
<!-- Pero como lo que queremos es minimizar más adelante con nuestros algoritmos de búsqueda de puntos de ruptura, lo que haremos es trabajar con el opuesto de este, lo que quedaría: -->

$$ n \left(1 - \log \left(\frac{n}{\sum_{i = 1}^{n} y_i} \right) \right) $$

Let's rename this cost function for an easier aplication on intervals $[a;b]$ of time series:
<!-- Para formalizar más la función de costo, utilizaremos la notación: -->

$$ c(y_{a:b}) = n \left(1 - \log \left(\frac{n}{\sum_{i = a}^{b} y_i} \right) \right) $$

where $n = b-a+1$.

# Question 3

Implement the optimal partition algorithm and test it on the data simulated in question 1.

## Answer 3

The following code is an implementation of  `Optimal Partition` algorithm, it receives as data:

- The time series
- The cost function
- The amount on change points

We then use the `OptimalPartition` algorithm to predict the change points on the previously simulated data, the results are shown below.

![Optimal Partition Approximate Change Points](img/approx_OP.png)

# Question 4

Implement the PELT algorithm and test it using the data simulated in question 1. You will set the value of the penalty weight as $log(n)$.

## Answer 4

For `PELT` algorithm implementation, we base our implementation on Conference's notes and the article **"Killick, R., Fearnhead, P., & Eckley, I. A. (2012, oct). Optimal detection of changepoints with a linear computational cost. Journal of the American Statistical Association"**. Is important to note that `PELT` uses the **Theorem 3.1** to rule out possible change points. This theorem expresses the following: 

<!-- A continuación se hace una implementación del algoritmo `PELT`. Nos basamos tanto en la implementación de Conferencia como la explicada en el artículo **"Killick, R., Fearnhead, P., & Eckley, I. A. (2012, oct). Optimal detection of changepoints with a linear computational cost. Journal of the American Statistical Association"**. Es importante señalar que PELT hace uso del **Teorema 3.1** de este artículo para descarta posibles puntos de cambios. Este Teorema expresa lo siguiente: -->

*Theorem 3.1*: We assume that when introducing a changepoint into a sequence of observations, the cost, $C$, of the sequence reduces. More formally, we assume there exists a constant $K$ such that for all $t\lt s \lt T$.

Then pseudo-code of our algorithm looks like:
<!-- Luego el pseudocódigo de nuestro algoritmo sería: -->

**Input**:
- `data` $= \{y_1,...,y_n\}$: set of points
- $c(.)$: cost function
- $\beta$: penalty cost
- `K`: constant that satisfies **Theorem 3.1**
- $n_{min}$: minimal interval size

**Initializations**:
- `F` = $(-\beta,..., n_{min}-1 = -\beta, 0,...,0)$: array of size `n`
- `R` = $\{0\}$: initial set of possible change points
- `CP` = $(0,...,0)$: array de puntos de cambios asociados a cada dato

**Pseudo-Code**:

- **for all** $t^* = n_{min},..., n-1$:  
    - $F[\widetilde{ t }] = \min_{t \in R  ~ \backslash ~ |\widetilde{ t }-t| \ge n_{min} }\{F(t) + c(y_{t:\widetilde{ t }})+ \beta\}$
    - $\overline{t} = argmin_{t \in R  ~ \backslash ~ |\widetilde{ t }-t| \ge n_{min} }\{F(t) + c(y_{t:\widetilde{ t }-1})+ \beta\}$
    - $CP[\widetilde{ t }] = \overline{t}$
    - $R^* = \{ \widetilde{ t } \} \cup \{ t \in R ~ \backslash ~ F[t] + c(y_{t:\widetilde{ t }-1}) + K < F[\widetilde{ t }]\}$
    - $R = R^*$
- **end for**
- `last = CP[n-1]`
- **while** `last != 0` :
    - **add** `last` **to** `changes_points`
    - `last = CP[last]`
- **end while**  

**Output**
- `change_points`: Change Points

We then use the `PELT` algorithm to predict the changepoints on the previously simulated data, and we take as:

- $\beta = \log(n)$
- $K = 0$ because like the article says, that is the value which satisfiesl **Theorem 3.1**

The results are shown below. 

![PELT Approximate Change Points](img/approx_PELT.png)

# Question 5

Test the PELT algorithm with other values of the penalty weight. Comment the results.

## Answer 5

Turning to choice of penalty, in practice by far the most common choice is one which is linear in the number of changepoints. Examples of such penalties include Akaike’s Information Criterion (AIC) where $\beta = 2p$ and Schwarz Information Criterion (SIC also known as BIC) where $\beta = p\log n$; $p$ is the
number of additional parameters introduced by adding a changepoint and $n$ is the size of time series. [Killick, R., Fearnhead, P. and Eckley, I.A. "Optimal detection of changepoints with a linear
computational cost". October 10, 2012.]

For a model with 1 parameter fit per segment, each change point adds two parameters to the model (1 for the segment parameter and 1 for the new change point). It means that $p =2$. 

With Penalty AIC:

![Penalty AIC](img/AIC.png)

With Penalty BIC:

![Penalty BIC](img/BIC.png)

## Complementary Analisis
A remarcable difference between `Optimal Pertition` and `PELT` algorithm is the time complexity. The first one is more expensive, a closer look to this fact on other simulated data will provide an experimental proove for this statement; that is teorycally explained by $O(n)$ boundaries of each algorithm.
<!-- Una notable diferencia en ambos algoritmos es la complejidad temporal de ambos -->

We create 10 lists of 3 changepoints for each time series length between 100 and 3000 skipping 20, for a total of 1450 lists of change points. The commented code bellow generates the lists calling `gen_many_change_points` and saves them in a **many_change_points_to_simulate.json** file. Now we just need to open the file and load data and avoid to compute again all test cases, for that is the uncommnted line below. 

Keeping $K=3$ changepoints, we simulate time series with length $n = 100, 120, 140, ... , 500$ with change points values taken from the lists created before and run $30$ times each algorithm. Then we compare the mean time result for each $n$ and plot it on the graph at the end of the document.  

<!-- Manteniendo $K=3$ puntos de cambios, simularemos $30$ corridas de cada algoritmo sobre un series de tiempo en un intervalo $n = 100, 120, 140, ..., 500$. Luego de esto, graficaremos la media del tiempo demorado para cada $n$. -->

Temporal Complexity:

![Temporal Complexity](img/crves.png)
