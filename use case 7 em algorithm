import numpy as np
from scipy.stats import binom

# Observed data: number of heads in 10 tosses (5 trials)
heads = np.array([5, 9, 8, 4, 7])
n_trials = len(heads)
n_tosses = 10

# Initialize parameters
theta_A = 0.6
theta_B = 0.5

# Number of EM iterations
iterations = 10

for i in range(iterations):

    # -------- E-STEP --------
    prob_A = binom.pmf(heads, n_tosses, theta_A)
    prob_B = binom.pmf(heads, n_tosses, theta_B)

    weight_A = prob_A / (prob_A + prob_B)
    weight_B = prob_B / (prob_A + prob_B)

    # -------- M-STEP --------
    theta_A = np.sum(weight_A * heads) / np.sum(weight_A * n_tosses)
    theta_B = np.sum(weight_B * heads) / np.sum(weight_B * n_tosses)

    print(f"Iteration {i+1}")
    print("Theta_A:", round(theta_A, 4))
    print("Theta_B:", round(theta_B, 4))
    print("-----------------------")

print("\nFinal Estimates:")
print("Theta_A =", round(theta_A, 4))
print("Theta_B =", round(theta_B, 4))
