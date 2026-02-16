def is_consistent(hypothesis, example_attributes):
    """
    Checks if a given hypothesis is consistent with an example.
    A hypothesis is consistent if, for each attribute, either the hypothesis attribute
    matches the example attribute, or the hypothesis attribute is '?'.
    """
    for i in range(len(hypothesis)):
        if hypothesis[i] == '0': # '0' means not yet generalized for S, so it's inconsistent with any example
            return False
        if hypothesis[i] != '?' and hypothesis[i] != example_attributes[i]:
            return False
    return True

def is_more_general(h1, h2):
    """
    Returns True if hypothesis h1 is more general than or equal to h2, and False otherwise.
    '?' is more general than any specific attribute value.
    """
    # h1 is more general or equal to h2 if for every attribute, h1's value
    # is either '?' or matches h2's value.
    for i in range(len(h1)):
        if h1[i] == '0': # '0' represents maximally specific, nothing is more general than '0'
            return False
        # If h1 has a specific value that doesn't match h2, h1 cannot be more general than h2
        if h1[i] != '?' and h1[i] != h2[i]:
            return False
    # Also, h1 must not be identical to h2 if h2 has a '0' (meaning h1 can't be more general than an uninitialized hypothesis)
    # This part should be handled by the specific '0' check above for h1[i]
    return True

def generalize_specific(hypothesis, positive_example_attributes):
    """
    Takes a specific hypothesis and a positive example, and returns a new hypothesis
    that is the least general generalization of the two. This involves replacing
    specific attribute values with '?' where they differ.
    """
    new_hypothesis = list(hypothesis)
    for i in range(len(hypothesis)):
        if new_hypothesis[i] == '0':  # '0' represents an uninitialized or empty attribute in S
            new_hypothesis[i] = positive_example_attributes[i]
        elif new_hypothesis[i] != positive_example_attributes[i]:
            new_hypothesis[i] = '?'
    return tuple(new_hypothesis)

def specialize_general(hypothesis, negative_example_attributes, all_attributes_values):
    """
    Takes a general hypothesis and a negative example, and returns a list of new hypotheses
    that are minimal specializations of the general hypothesis, making it inconsistent
    with the negative example.
    `all_attributes_values` is a list of lists, where each inner list contains all possible
    values for that attribute (excluding '?').
    """
    specializations = set()
    for i in range(len(hypothesis)):
        if hypothesis[i] == '?': # If the attribute is '?', we can specialize it
            for value in all_attributes_values[i]:
                # Create a specialization by fixing this '?' to a value different from the negative example
                if value != negative_example_attributes[i]:
                    new_hypothesis = list(hypothesis)
                    new_hypothesis[i] = value
                    specializations.add(tuple(new_hypothesis))
    return list(specializations)


def candidate_elimination_stepwise(examples, attribute_names, all_attributes_values):
    """
    Implements the core Candidate Elimination algorithm, showing evolution of S and G.
    `examples` is a list of tuples: (attributes_tuple, label).
    `attribute_names` is a list of strings representing the names of the attributes.
    `all_attributes_values` is a list of lists, where each inner list contains all possible
    values for that attribute (excluding '?').
    """
    num_attributes = len(attribute_names)
    S = {('0',) * num_attributes} # Most specific: covers no examples initially
    G = {('?',) * num_attributes} # Most general: covers all examples initially

    print(f"Initial S: {S}")
    print(f"Initial G: {G}\n")

    for i, (example_attributes, label) in enumerate(examples):
        print(f"--- Processing Example {i+1}: {example_attributes}, Label: {label} ---")

        if label == 'yes':  # Positive example
            # Remove from G any hypothesis g that is inconsistent with the example
            G = {g for g in G if is_consistent(g, example_attributes)}

            # For each hypothesis s in S that is inconsistent with the example
            s_to_remove = set()
            s_to_add = set()
            for s in S:
                if not is_consistent(s, example_attributes): # If s doesn't cover the positive example
                    s_to_remove.add(s)
                    # Generalize s to cover the positive example
                    generalized_s = generalize_specific(s, example_attributes)

                    # Add the generalized hypothesis to S, but only if it is more specific than some hypothesis in G.
                    if any(is_more_general(g_hyp, generalized_s) for g_hyp in G): # This condition is to ensure the new S is within the bounds of G
                        s_to_add.add(generalized_s)
            S.difference_update(s_to_remove)
            S.update(s_to_add)

            # Prune S: remove any hypothesis s1 from S if there exists s2 in S such that s2 is strictly more specific than s1.
            S_cleaned = set()
            for s1 in S:
                is_maximally_specific = True
                for s2 in S:
                    if s1 != s2 and is_more_general(s1, s2): # If s1 is MORE GENERAL than s2, then s1 is NOT maximally specific (s2 is more specific).
                        is_maximally_specific = False
                        break
                if is_maximally_specific:
                    S_cleaned.add(s1)
            S = S_cleaned

            # Filter G: remove any hypothesis g from G that is not more general than ALL hypotheses in S
            G = {g for g in G if all(is_more_general(g, s) for s in S)}

        else:  # Negative example
            # Remove from S any hypothesis s that is consistent with the example
            S = {s for s in S if not is_consistent(s, example_attributes)}

            # For each hypothesis g in G that is consistent with the example (but should be inconsistent)
            g_to_remove = set()
            g_to_add = set()
            for g in G:
                if is_consistent(g, example_attributes): # g is consistent with a negative example, so it needs to be specialized
                    g_to_remove.add(g)
                    # Specialize g to make it inconsistent with the negative example
                    specialized_gs = specialize_general(g, example_attributes, all_attributes_values)

                    # Add the specialized hypotheses to G, but only if they are more general than ALL hypotheses in S.
                    # This check is crucial for maintaining the version space boundaries.
                    for sg in specialized_gs:
                        # Check if S is empty before checking `all` conditions
                        if not S or all(is_more_general(sg, s_hyp) for s_hyp in S): # Make sure specialization is not too specific, and covers all current S
                            g_to_add.add(sg)
            G.difference_update(g_to_remove)
            G.update(g_to_add)

            # Prune G: Remove any hypothesis g1 from G if there exists g2 in G such that g2 is strictly more general than g1.
            G_cleaned = set()
            for g1 in G:
                is_maximally_general = True
                for g2 in G:
                    if g1 != g2 and is_more_general(g2, g1): # If g2 is MORE GENERAL than g1, then g1 is NOT maximally general (g2 is more general).
                        is_maximally_general = False
                        break
                if is_maximally_general:
                    G_cleaned.add(g1)
            G = G_cleaned

            # Filter S: remove any hypothesis s from S that is not more specific than ALL hypotheses in G
            S = {s for s in S if all(is_more_general(g, s) for g in G)}

        # If S or G becomes empty, it implies the version space has collapsed, often due to inconsistent data
        if not S or not G:
            print("Version space has collapsed (S or G became empty).")
            # For demonstration, we can continue to show the collapse remains.

        print(f"Current S: {S}")
        print(f"Current G: {G}\n")

    return S, G


def classify_new_example(example_attributes, S_final, G_final):
    """
    Classifies a new example based on the final S and G sets.
    Returns 'yes', 'no', or 'ambiguous' according to the specified logic.
    """
    if not S_final and not G_final:
        return 'ambiguous'

    if not S_final and G_final: # S is empty, G is not empty
        # 'yes' if consistent with *all* hypotheses in G_final, else 'no'.
        if all(is_consistent(g, example_attributes) for g in G_final):
            return 'yes'
        else:
            return 'no'

    if not G_final and S_final: # G is empty, S is not empty
        # 'no' if inconsistent with *any* hypothesis in S_final,
        # 'yes' if consistent with all hypotheses in S_final.
        if any(not is_consistent(s, example_attributes) for s in S_final):
            return 'no'
        elif all(is_consistent(s, example_attributes) for s in S_final):
            return 'yes'
        else:
            # This case shouldn't strictly happen if the above two conditions are exhaustive due to how `any` and `all` relate.
            # But for robustness, if it's consistent with some but not all, it's ambiguous.
            return 'ambiguous'

    # Both S_final and G_final contain hypotheses
    all_s_consistent = all(is_consistent(s, example_attributes) for s in S_final)
    all_g_consistent = all(is_consistent(g, example_attributes) for g in G_final)

    all_s_inconsistent = all(not is_consistent(s, example_attributes) for s in S_final)
    all_g_inconsistent = all(not is_consistent(g, example_attributes) for g in G_final)

    if all_s_consistent and all_g_consistent:
        return 'yes'
    elif all_s_inconsistent and all_g_inconsistent:
        return 'no'
    else:
        return 'ambiguous'


# Re-run the core algorithm to get the final S and G after the corrections
# This call will also print the stepwise evolution.
S_final, G_final = candidate_elimination_stepwise(examples, attribute_names, all_attributes_values)

print("\n--- Final Version Space from previous step ---")
print("Final Most Specific Hypotheses (S):")
for s in S_final:
    print(f"  {s}")

print("Final Most General Hypotheses (G):")
for g in G_final:
    print(f"  {g}")


print("\n--- Classifying New Examples ---")

# Create new, unseen patient symptom combinations
new_examples = [
    ('high', 'yes', 'yes'),  # Positive training example, should classify 'yes' if concept learned correctly
    ('normal', 'no', 'no'),  # Negative training example, should classify 'no' if concept learned correctly
    ('high', 'no', 'no'),   # Negative training example, should classify 'no'
    ('high', '?', 'yes'),    # Hypothetical example, might be ambiguous or classified
    ('normal', 'yes', 'no')  # Another negative training example
]

for i, new_ex in enumerate(new_examples):
    classification = classify_new_example(new_ex, S_final, G_final)
    print(f"New Example {i+1}: {new_ex} -> Classified as: {classification}")
