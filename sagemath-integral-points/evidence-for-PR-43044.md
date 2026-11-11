I would like to contribute reproducible regression cases and lattice evidence to the focused revision requested above.

Fresh Sage 10.9 runs reproduce all twelve missing x-coordinates on the eleven curves in #27015. Three small exact reproductions are:

* 1701d1, `[1,-1,1,-11,16]`: for P=(2,-2), 7P=(20829482,-95074767761). The installed method uses coefficient bound 6 and omits that x-coordinate.
* 2082a1, `[1,0,1,-118,584]`: for P=(13,29), 13P=(507525709,-11433961056931). The installed method uses bound 12 and omits it.
* 1848f1, `[0,1,0,44,-847]`: for P=(44,-297), 5P=(176,2343) and 6P=(179,-2403). Both are omitted even though the coefficient bound is 12 and both x-coordinates are inside the computed x-bound. This also exposes the separate final-search precision problem.

The multiples were checked with exact Sage arithmetic and separately written rational group arithmetic. Cremona already identified the failed inequality and scale remedy in #10973, comment 45 (2013).

There is also an exact counterexample to the old Gram–Schmidt expression: the 0.99-LLL-reduced row basis

    [[2000,0,0], [1000,1722,0], [0,861,1484]]

has squared GS lengths 4000000, 2965284 and 2202256. The old expression gives 2968000, but the third row has squared length 2943577. The minimum exact squared GS length is a valid bound for nonzero vectors of the homogeneous lattice; an inhomogeneous target requires a separate distance argument.

A separate experimental reduction patch rebuilds the lattice with increased scale and Arb precision, checks the actual rounding errors and strict inequality, and raises after capped retries. Its copied method passed all 27 examples in the extracted method/helper doctest file with `--long` and 42 sampled ecdata comparisons, including all eleven problem curves. The original full-run log reports 317 matches, and all 317 expected inputs agree with freshly downloaded ecdata. These checks used Sage 10.9 with methods copied from develop `026e931e47064f0a7fc095580119365518bfe7c0`; a full Sage 11.0 build was not tested. The patch leaves the legacy initial constants and approximate final enumeration in place.

I would be glad to contribute the regression cases, exact counterexample and test scripts to the revision while preserving the existing supplied-generator behaviour. These checks concern the reproductions and the experimental patch; I have not independently validated this PR's implementation.

This work was prepared with OpenAI Codex AI assistance and has not received independent human proof review. The Carlson/Arb design in the experimental patch was informed by Manvir Jaswal's GPL implementation in this PR at `9be54a7fd40ae95a495d91580434bd2aedf0faff`. Credit for the original defect report belongs to John Cremona.
