# Assignment 4 Errata

  * Under ‘Advanced entity retrieval: PRMS’, under ‘Retrieval method’, the Dirichlet smoothing specifies that ’$\mu_i$ is the field-specific smoothing parameter (set to 1000)’. However, the corresponding global variable is set in the code cell below as MU = 100. In the context of A4, the coded value of 100 is correct, and the description’s mention of ‘set to 1000’ can be ignored.
  * There is an error in the field mapping probabilities test. This part of the exercise will be corrected manually.
  * Getting less than 2500 items indexed will not be penalized.

There are two issues raised that have been addressed in detail below, the number of entities to be indexed and the field mapping probabilities. 

## (1) Changing numbers of entities to be indexed

Regarding the number of entities to be indexed, it appears the updating of the subpages of 
`'Lists_of_musicians'` has been happening more frequently than anticipated when creating the assignment,
and thus the tests will need to be updated. 

After running through the code in the reference solution this afternoon (13:30), there were 2748 unique entities indexed. 

The affected test cells are updated and provided below. You can insert and run these as 
code cells in your notebook, but do remove them before submission. Tests will be used during grading based on updates that will be current at that time. 

This is the current, updated test cell following `bulk_index`:

```
# Test index
tv_1 = es.termvectors(index=INDEX_NAME, id='Al Green', fields='catch_all')
assert tv_1['term_vectors']['catch_all']['terms']['1946']['term_freq'] == 23

tv_2 = es.termvectors(index=INDEX_NAME, id='Runhild Gammelsæter', fields='attributes')
assert tv_2['term_vectors']['attributes']['terms']['khlyst']['term_freq'] == 1

tv_3 = es.termvectors(index=INDEX_NAME, id='Music of Belarus', fields='description')
assert tv_3['term_vectors']['description']['terms']['kazakhstan']['term_freq'] == 1

tv_4 = es.termvectors(index=INDEX_NAME, id='MC HotDog', fields=['types', 'description'])
assert tv_4['term_vectors']['description']['terms']['microphon']['term_freq'] == 1

tv_5 = es.termvectors(index=INDEX_NAME, id='Deadmau5', fields=['types', 'description', 'related_entities'])
assert tv_5['term_vectors']['description']['terms']['italiano']['term_freq'] == 1
```


The updated test cell for field mapping probabilities is:

```
# Tests for field mapping probabilities
clm_3 = CollectionLM(es, analyze_query(es, 'gospel soul'))
Pf_t_3_1 = get_term_mapping_probs(es, clm_3, 'gospel')
assert Pf_t_3_1['description'] == pytest.approx(0.19345, abs=1e-5)
assert Pf_t_3_1['attributes'] == pytest.approx(0.13442, abs=1e-5)
assert Pf_t_3_1['related_entities'] == pytest.approx(0.30600, abs=1e-5)

Pf_t_3_2 = get_term_mapping_probs(es, clm_3, 'soul')
assert Pf_t_3_2['names'] == pytest.approx(0.01502, abs=1e-5)
assert Pf_t_3_2['types'] == pytest.approx(0.10632, abs=1e-5)
assert Pf_t_3_2['catch_all'] == pytest.approx(0.16552, abs=1e-5)
```

The updated test cell for `prms_retrieval` is 

```
# Tests for PRMS retrieval

prms_query_1 = 'winter snow'
prms_retrieval_1 = prms_retrieval(es, prms_query_1)
assert prms_retrieval_1[:5] == ['Snow (musician)', 'Kurt Winter', 'Hank Snow', 'Derek Miller', 'Richard Manuel']

prms_query_2 = 'summer sun'
prms_retrieval_2 = prms_retrieval(es, prms_query_2)
assert prms_retrieval_2[:5] == ['Joseph Summers (musician)', 'Sun Nan', 'Stefanie Sun', 'Virgil Sturgill', 'Robert Turner (composer)']

prms_query_3 = 'freedom jazz'
prms_retrieval_3 = prms_retrieval(es, prms_query_3)
assert prms_retrieval_3[:5] == ['Paul Bley', 'Stéphane Galland', 'Saifo', 'Christy Sutherland', 'K.d. lang']
```

and the final test cell comparing the above with baseline retrieval needs no update. 

None of the other test cells in A4 need updating at this time. 

## (2) Field mapping probabilities

There was a small notational inconsistency in the description and variable 
names of the code for `get_term_mapping_probabilities`.

The correct description should be 
"
Implement the function `get_term_mapping_probs` to return $P(f|t)$ of all fields $f$ 
for a given term $t$ according to the description above of how PRMS extends the MLM retrieval model. 
"
and the Dictionary of mapping probabilities to be returned by `get_term_mapping_probabilities` 
should properly be named `Pf_t` rather than `Pt_f`, reflecting that mapping probability is the probability 
(normalized over the probabilities of all fields) that a given field $f$  should be the 
field to which a term $t$ would be mapped. 

You may consider this your starting point for implementing that solution, if you find it helpful:
```
def get_term_mapping_probs(es, clm, term):
    """PRMS: For a single term, find their mapping probabilities for all fields.
    
    Arguments:
        es: An active Elasticsearch instance.
        clm: Collection language model instance.
        term: A single-term string. 
        
    Returns:
        Dictionary of mapping probabilities for the fields.
    """
    Pf_t = {}
    # YOUR CODE HERE
    raise NotImplementedError()
    return Pf_t
```

Use the longer mathematical description under the heading "Retrieval method" in the 
A4 notebook and note that the mapping probability is $P(f|t)$. 