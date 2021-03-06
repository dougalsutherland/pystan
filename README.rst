PyStan: The Python Interface to Stan
====================================

.. image:: https://travis-ci.org/ariddell/pystan.png
        :target: https://travis-ci.org/ariddell/pystan

PyStan has an interface similar to that of RStan. For an introduction to Stan
and RStan see `http://mc-stan.org/ <http://mc-stan.org/>`_ and `RStan Getting
Started <https://code.google.com/p/stan/wiki/RStanGettingStarted>`_.

Important links
---------------

- Source code repo: https://github.com/ariddell/pystan
- HTML documentation: http://pystan.readthedocs.org
- Issue tracker: https://github.com/ariddell/pystan/issues
- Stan: http://mc-stan.org/

Installation
------------

`NumPy  <http://www.numpy.org/>`_ and `Cython <http://www.cython.org/>`_
are required.

::

   git clone https://github.com/ariddell/pystan.git
   cd pystan
   python setup.py install

Example
-------

::

    import pystan
    import numpy as np

    schools_code = """
    data {
        int<lower=0> J; // number of schools
        real y[J]; // estimated treatment effects
        real<lower=0> sigma[J]; // s.e. of effect estimates
    }
    parameters {
        real mu;
        real<lower=0> tau;
        real eta[J];
    }
    transformed parameters {
        real theta[J];
        for (j in 1:J)
        theta[j] <- mu + tau * eta[j];
    }
    model {
        eta ~ normal(0, 1);
        y ~ normal(theta, sigma);
    }
    """

    schools_dat = {'J': 8,
                   'y': [28,  8, -3,  7, -1,  1, 18, 12],
                   'sigma': [15, 10, 16, 11,  9, 11, 10, 18]}

    fit = pystan.stan(model_code=schools_code, data=schools_dat,
                      iter=1000, chains=4)

    eta = fit.extract(permuted=True)['eta']
    np.mean(eta, axis=0)
