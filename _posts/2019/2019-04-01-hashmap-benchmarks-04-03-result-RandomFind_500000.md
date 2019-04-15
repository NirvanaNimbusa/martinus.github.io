---
layout: post
title: Hashmaps Benchmarks - Find 1-500k Entries
subtitle: Finding the Fastest, Memory Efficient Hashmap
bigimg: /img/2019/X-15_in_flight_small.jpg
---

## Table of Contents

* [Overview](/2019/04/01/hashmap-benchmarks-01-overview/)
* Construction Benchmarks
   * [Construction & Destruction](/2019/04/01/hashmap-benchmarks-02-01-result-CtorDtorEmptyMap/)
   * [Construction & Insert 1 Element & Destruction](/2019/04/01/hashmap-benchmarks-02-02-result-CtorDtorSingleEntryMap/)
* Modifying Benchmarks
   * [Insert & Erase 100M Entries](/2019/04/01/hashmap-benchmarks-03-01-result-InsertHugeInt/)
   * [Insert or Access, Varying Probability](/2019/04/01/hashmap-benchmarks-03-02-result-RandomDistinct2/)
   * [Insert & Erase](/2019/04/01/hashmap-benchmarks-03-03-result-RandomInsertErase/)
   * [Insert & Erase Strings](/2019/04/01/hashmap-benchmarks-03-04-result-RandomInsertEraseStrings/)
* Accessing
   * [Find 1-200 Entries](/2019/04/01/hashmap-benchmarks-04-01-result-RandomFind_200/)
   * [Find 1-2000 Entries](/2019/04/01/hashmap-benchmarks-04-02-result-RandomFind_2000/)
   * **[Find 1-500k Entries](/2019/04/01/hashmap-benchmarks-04-03-result-RandomFind_500000/)** 👈
   * [Iterating](/2019/04/01/hashmap-benchmarks-04-04-result-IterateIntegers/)
* [Conclusion](/2019/04/01/hashmap-benchmarks-05-conclusion/)


----

Almost the same as [Construction & Destruction](/2019/04/01/hashmap-benchmarks-CtorDtorEmptyMap/), but this time a single element is inserted. All maps that do lazy initialization are now forced to actually provide storage memory, and they have to calculate the hash function at least once to find a proper slot.

## Benchmark Code

The full benchmark code is this: 

```cpp
for (size_t n = 0; n < 50'000'000; ++n) {
    Map<int, int> map;
    map[123];
    result += map.size();
}
```

After the loop is done, the variable `result` is used in a verifcation step. It should always be 50'000'000.

## Results

Now it gets a lot more interesting.

<script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
<div id="id_ac9de32b" style="height:250em"></div>
<script>
    var colors = Plotly.d3.scale.category10().range();
    var m0y = [ "std::unordered_map", "boost::unordered_map", "eastl::hash_map", "phmap::<br>parallel_node_hash_map", "boost::multi_index::<br>hashed_unique", "phmap::<br>parallel_flat_hash_map", "spp::sparse_hash_map", "folly::F14ValueMap", "tsl::hopscotch_map", "folly::F14NodeMap", "tsl::robin_map", "<b>tsl::sparse_map</b>", "ska::bytell_hash_map", "emilib1::HashMap", "robin_hood::<br>unordered_flat_map", "<b>robin_hood::<br>unordered_node_map</b>", "<b>absl::node_hash_map</b>", "<b>phmap::node_hash_map</b>", "<b>absl::flat_hash_map</b>", "<b>phmap::flat_hash_map</b>"];
    var m1y = [ "std::unordered_map", "boost::unordered_map", "eastl::hash_map", "phmap::<br>parallel_node_hash_map", "boost::multi_index::<br>hashed_unique", "phmap::<br>parallel_flat_hash_map", "spp::sparse_hash_map", "folly::F14ValueMap", "tsl::hopscotch_map", "folly::F14NodeMap", "tsl::robin_map", "<b>tsl::sparse_map</b>", "ska::bytell_hash_map", "emilib1::HashMap", "robin_hood::<br>unordered_flat_map", "<b>robin_hood::<br>unordered_node_map</b>", "<b>absl::node_hash_map</b>", "<b>phmap::node_hash_map</b>", "<b>absl::flat_hash_map</b>", "<b>phmap::flat_hash_map</b>"];
    var m2y = [ "std::unordered_map", "boost::unordered_map", "eastl::hash_map", "phmap::<br>parallel_node_hash_map", "boost::multi_index::<br>hashed_unique", "phmap::<br>parallel_flat_hash_map", "spp::sparse_hash_map", "tsl::hopscotch_map", "folly::F14ValueMap", "tsl::robin_map", "folly::F14NodeMap", "<b>tsl::sparse_map</b>", "ska::bytell_hash_map", "emilib1::HashMap", "robin_hood::<br>unordered_flat_map", "<b>robin_hood::<br>unordered_node_map</b>", "<b>absl::node_hash_map</b>", "<b>phmap::node_hash_map</b>", "<b>absl::flat_hash_map</b>", "<b>phmap::flat_hash_map</b>"];
    var m3y = [ "std::unordered_map", "boost::unordered_map", "eastl::hash_map", "phmap::<br>parallel_node_hash_map", "boost::multi_index::<br>hashed_unique", "phmap::<br>parallel_flat_hash_map", "spp::sparse_hash_map", "tsl::hopscotch_map", "folly::F14ValueMap", "tsl::robin_map", "folly::F14NodeMap", "emilib1::HashMap", "<b>tsl::sparse_map</b>", "ska::bytell_hash_map", "absl::node_hash_map", "phmap::node_hash_map", "<b>robin_hood::<br>unordered_node_map</b>", "<b>robin_hood::<br>unordered_flat_map</b>", "<b>absl::flat_hash_map</b>", "<b>phmap::flat_hash_map</b>"];
    var m4y = [ "tsl::sparse_map", "tsl::robin_map", "tsl::hopscotch_map", "spp::sparse_hash_map", "robin_hood::<br>unordered_node_map", "robin_hood::<br>unordered_flat_map", "phmap::<br>parallel_node_hash_map", "phmap::<br>parallel_flat_hash_map", "phmap::node_hash_map", "phmap::flat_hash_map", "emilib1::HashMap", "absl::node_hash_map", "absl::flat_hash_map", "std::unordered_map", "boost::unordered_map", "<b>eastl::hash_map</b>", "boost::multi_index::<br>hashed_unique", "folly::F14ValueMap", "<b>folly::F14NodeMap</b>", "<b>ska::bytell_hash_map</b>"];
    var measurement_names = [ "0% success, 0x00000000ffffffff", "0% success, 0xffffffff00000000", "25% success, 0x00000000ffffffff", "25% success, 0xffffffff00000000", "50% success, 0x00000000ffffffff", "50% success, 0xffffffff00000000", "75% success, 0x00000000ffffffff", "75% success, 0xffffffff00000000", "100% success, 0x00000000ffffffff", "100% success, 0xffffffff00000000" ];

    var data = [
        { x: [ 5.56816e-08, 4.9191700000000005e-08, 3.2362900000000005e-08, 2.60524e-08, 2.5136e-08, 2.0887400000000002e-08, 2.0654200000000002e-08, 1.3406470000000002e-08, 1.7136000000000002e-08, 1.1782319999999999e-08, 1.6390060000000002e-08, 1.3710700000000001e-08, 1.454759e-08, 1.4131950000000001e-08, 7.76349e-09, 7.857080000000001e-09, 9.40206e-09, 9.264260000000001e-09, 8.281190000000001e-09, 8.25094e-09 ],
          y: m0y, name: measurement_names[0] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[0], },
        },
        { x: [ 5.5817500000000003e-08, 4.922480000000001e-08, 3.2394000000000005e-08, 2.60998e-08, 2.51767e-08, 2.08452e-08, 2.0711800000000003e-08, 1.3362160000000002e-08, 1.725798e-08, 1.198599e-08, 1.6411420000000002e-08, 1.3751160000000002e-08, 1.4544950000000001e-08, 1.416715e-08, 7.84265e-09, 7.93915e-09, 9.555229999999999e-09, 9.40788e-09, 8.32616e-09, 8.30022e-09 ],
          y: m0y, name: measurement_names[1] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[1], },
        },
        { x: [ 6.18015e-08, 5.31378e-08, 3.8061100000000005e-08, 3.27902e-08, 2.9475900000000003e-08, 2.6874600000000002e-08, 2.6798300000000002e-08, 1.9526390000000004e-08, 2.08971e-08, 1.9741750000000002e-08, 2.10681e-08, 1.9626429999999998e-08, 1.826571e-08, 1.662313e-08, 1.3214850000000001e-08, 1.3265450000000001e-08, 1.5475490000000002e-08, 1.528674e-08, 1.294014e-08, 1.28224e-08 ],
          y: m0y, name: measurement_names[2] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[2], },
        },
        { x: [ 6.19378e-08, 5.300850000000001e-08, 3.8062899999999996e-08, 3.280340000000001e-08, 2.9435800000000002e-08, 2.6837e-08, 2.68177e-08, 1.948048e-08, 2.12752e-08, 1.9824550000000003e-08, 2.10789e-08, 1.9690240000000003e-08, 1.825968e-08, 1.6635640000000002e-08, 1.324857e-08, 1.3324360000000001e-08, 1.54518e-08, 1.529168e-08, 1.294436e-08, 1.284934e-08 ],
          y: m0y, name: measurement_names[3] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[3], },
        },
        { x: [ 5.891400000000001e-08, 5.2507900000000004e-08, 3.76295e-08, 3.43846e-08, 2.9050700000000004e-08, 2.7701e-08, 2.62059e-08, 2.3599000000000002e-08, 2.35213e-08, 2.3291100000000002e-08, 2.15388e-08, 2.0024300000000004e-08, 1.876206e-08, 1.812942e-08, 1.677011e-08, 1.575506e-08, 1.607125e-08, 1.59459e-08, 1.4388950000000001e-08, 1.4200760000000002e-08 ],
          y: m0y, name: measurement_names[4] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[4], },
        },
        { x: [ 5.8862e-08, 5.245900000000001e-08, 3.76931e-08, 3.4376800000000006e-08, 2.8994500000000002e-08, 2.7710200000000002e-08, 2.62568e-08, 2.3593200000000002e-08, 2.3507900000000002e-08, 2.32712e-08, 2.15732e-08, 2.0054000000000002e-08, 1.879003e-08, 1.815322e-08, 1.678923e-08, 1.576371e-08, 1.609576e-08, 1.5953060000000003e-08, 1.4420680000000001e-08, 1.4189740000000002e-08 ],
          y: m0y, name: measurement_names[5] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[5], },
        },
        { x: [ 5.0249900000000004e-08, 4.73851e-08, 3.3251400000000006e-08, 3.07778e-08, 2.50818e-08, 2.4650600000000002e-08, 2.24773e-08, 2.4559000000000002e-08, 2.2219400000000004e-08, 2.3360300000000002e-08, 1.8793760000000002e-08, 1.6800790000000002e-08, 1.7109180000000002e-08, 1.588706e-08, 1.8036350000000003e-08, 1.670982e-08, 1.4281220000000001e-08, 1.4009580000000001e-08, 1.3053820000000001e-08, 1.29399e-08 ],
          y: m0y, name: measurement_names[6] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[6], },
        },
        { x: [ 5.019260000000001e-08, 4.7335200000000004e-08, 3.3166200000000005e-08, 3.0774200000000006e-08, 2.5043700000000003e-08, 2.4730400000000002e-08, 2.2513500000000002e-08, 2.45506e-08, 2.21087e-08, 2.3273600000000002e-08, 1.881302e-08, 1.683293e-08, 1.706991e-08, 1.589391e-08, 1.806269e-08, 1.675333e-08, 1.427127e-08, 1.397862e-08, 1.3061890000000001e-08, 1.295562e-08 ],
          y: m0y, name: measurement_names[7] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[7], },
        },
        { x: [ 3.92513e-08, 3.8863900000000004e-08, 2.5568900000000003e-08, 2.52796e-08, 1.801012e-08, 2.0240400000000003e-08, 1.8180240000000002e-08, 2.26752e-08, 1.758992e-08, 2.0438900000000003e-08, 1.4389520000000001e-08, 1.3175450000000002e-08, 1.312473e-08, 1.219928e-08, 1.616787e-08, 1.4696570000000001e-08, 1.173082e-08, 1.1678060000000001e-08, 1.143192e-08, 1.1302400000000001e-08 ],
          y: m0y, name: measurement_names[8] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[8], },
        },
        { x: [ 3.92416e-08, 3.88433e-08, 2.55193e-08, 2.5267600000000003e-08, 1.7964480000000002e-08, 2.02472e-08, 1.8177639999999997e-08, 2.2617400000000002e-08, 1.7403600000000003e-08, 2.0559e-08, 1.4371430000000002e-08, 1.314997e-08, 1.3021770000000002e-08, 1.2204960000000001e-08, 1.618193e-08, 1.469964e-08, 1.174861e-08, 1.1625640000000002e-08, 1.13947e-08, 1.1274780000000001e-08 ],
          y: m0y, name: measurement_names[9] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[9], },
            textposition: 'outside',
            text: [ "53.2ns avg<br>20MB", "48.2ns avg<br>23MB", "33.4ns avg<br>11MB", "29.9ns avg<br>17MB", "25.3ns avg<br>27MB", "24.1ns avg<br>12MB", "22.9ns avg<br>0.0MB", "20.7ns avg<br>22MB", "20.3ns avg<br>37MB", "19.8ns avg<br>20MB", "18.4ns avg<br>37MB", "<b>16.7ns avg<br>0.0MB</b>", "16.3ns avg<br>23MB", "15.4ns avg<br>37MB", "14.4ns avg<br>23MB", "<b>13.7ns avg<br>9.5MB</b>", "<b>13.4ns avg<br>21MB</b>", "<b>13.2ns avg<br>23MB</b>", "<b>12.0ns avg<br>24MB</b>", "<b>11.9ns avg<br>25MB</b>" ],
        },
        { x: [ 5.670530000000001e-08, 5.0144200000000006e-08, 3.25034e-08, 2.6275800000000002e-08, 2.6154700000000002e-08, 2.12192e-08, 2.05207e-08, 1.366231e-08, 1.695892e-08, 1.1942340000000001e-08, 1.517302e-08, 1.410984e-08, 1.5164500000000003e-08, 1.479915e-08, 8.63383e-09, 8.695830000000001e-09, 9.948439999999999e-09, 9.86956e-09, 9.02647e-09, 8.72514e-09 ],
          y: m1y, name: measurement_names[0] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[0], },
        },
        { x: [ 5.6743700000000005e-08, 5.02113e-08, 3.26325e-08, 2.6449000000000004e-08, 2.6256900000000003e-08, 2.11622e-08, 2.05121e-08, 1.3584580000000002e-08, 1.691823e-08, 1.213221e-08, 1.518473e-08, 1.4125640000000001e-08, 1.520076e-08, 1.4801570000000001e-08, 8.63763e-09, 8.681400000000002e-09, 1.0031539999999999e-08, 9.954240000000001e-09, 9.03485e-09, 8.742960000000001e-09 ],
          y: m1y, name: measurement_names[1] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[1], },
        },
        { x: [ 6.17371e-08, 5.39661e-08, 3.78904e-08, 3.3084200000000005e-08, 3.0034500000000003e-08, 2.7177600000000005e-08, 2.70485e-08, 1.9804500000000004e-08, 2.13922e-08, 1.985769e-08, 1.9546230000000002e-08, 1.9971320000000003e-08, 1.905747e-08, 1.789532e-08, 1.424819e-08, 1.4304659999999999e-08, 1.643527e-08, 1.627038e-08, 1.382252e-08, 1.358254e-08 ],
          y: m1y, name: measurement_names[2] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[2], },
        },
        { x: [ 6.18046e-08, 5.4064e-08, 3.79325e-08, 3.31262e-08, 3.00041e-08, 2.715e-08, 2.70314e-08, 1.980478e-08, 2.12441e-08, 1.985877e-08, 1.9544860000000002e-08, 1.996556e-08, 1.903787e-08, 1.7913070000000002e-08, 1.426541e-08, 1.4260880000000001e-08, 1.641691e-08, 1.628464e-08, 1.379487e-08, 1.356202e-08 ],
          y: m1y, name: measurement_names[3] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[3], },
        },
        { x: [ 5.92919e-08, 5.28767e-08, 3.80641e-08, 3.46994e-08, 2.9542e-08, 2.8206600000000004e-08, 2.65244e-08, 2.40381e-08, 2.2152800000000002e-08, 2.35415e-08, 2.0155200000000002e-08, 2.0506500000000002e-08, 1.975288e-08, 1.8289180000000004e-08, 1.790753e-08, 1.6887660000000003e-08, 1.700214e-08, 1.688972e-08, 1.491293e-08, 1.494046e-08 ],
          y: m1y, name: measurement_names[4] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[4], },
        },
        { x: [ 5.93035e-08, 5.28679e-08, 3.8032700000000004e-08, 3.47148e-08, 2.95229e-08, 2.8214200000000005e-08, 2.65262e-08, 2.40044e-08, 2.2180600000000003e-08, 2.3528400000000002e-08, 2.01946e-08, 2.05061e-08, 1.9726190000000002e-08, 1.832631e-08, 1.7884630000000002e-08, 1.698647e-08, 1.699265e-08, 1.686918e-08, 1.491535e-08, 1.493038e-08 ],
          y: m1y, name: measurement_names[5] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[5], },
        },
        { x: [ 5.14413e-08, 4.808840000000001e-08, 3.33467e-08, 3.1390000000000005e-08, 2.55647e-08, 2.5398e-08, 2.28712e-08, 2.47275e-08, 2.15771e-08, 2.34219e-08, 1.760106e-08, 1.726026e-08, 1.743e-08, 1.6712870000000004e-08, 1.946428e-08, 1.800535e-08, 1.4794900000000002e-08, 1.463824e-08, 1.3609960000000001e-08, 1.3491320000000001e-08 ],
          y: m1y, name: measurement_names[6] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[6], },
        },
        { x: [ 5.1455700000000005e-08, 4.80286e-08, 3.3384500000000004e-08, 3.1376400000000004e-08, 2.5576400000000002e-08, 2.5409600000000002e-08, 2.28847e-08, 2.4773900000000002e-08, 2.1334700000000002e-08, 2.33007e-08, 1.757557e-08, 1.7272950000000002e-08, 1.740198e-08, 1.672872e-08, 1.948639e-08, 1.8011500000000002e-08, 1.4770960000000001e-08, 1.4629300000000001e-08, 1.362373e-08, 1.34788e-08 ],
          y: m1y, name: measurement_names[7] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[7], },
        },
        { x: [ 4.0886600000000006e-08, 3.92395e-08, 2.60126e-08, 2.6215e-08, 1.8359320000000002e-08, 2.1103000000000004e-08, 1.8526870000000003e-08, 2.2867900000000005e-08, 1.754729e-08, 2.0414100000000002e-08, 1.405655e-08, 1.3647950000000001e-08, 1.379911e-08, 1.2947700000000002e-08, 1.7307229999999998e-08, 1.595712e-08, 1.2144410000000001e-08, 1.207002e-08, 1.189442e-08, 1.1758800000000002e-08 ],
          y: m1y, name: measurement_names[8] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[8], },
        },
        { x: [ 4.07945e-08, 3.9286600000000005e-08, 2.5979500000000004e-08, 2.6234400000000004e-08, 1.8347280000000002e-08, 2.11464e-08, 1.851455e-08, 2.2897100000000002e-08, 1.752276e-08, 2.07253e-08, 1.407182e-08, 1.364076e-08, 1.375164e-08, 1.2951730000000002e-08, 1.732268e-08, 1.5948240000000002e-08, 1.214129e-08, 1.2090940000000001e-08, 1.1850940000000002e-08, 1.175264e-08 ],
          y: m1y, name: measurement_names[9] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[9], },
            textposition: 'outside',
            text: [ "54.0ns avg<br>22MB", "48.9ns avg<br>22MB", "33.6ns avg<br>11MB", "30.4ns avg<br>17MB", "25.9ns avg<br>27MB", "24.6ns avg<br>12MB", "23.1ns avg<br>0.0MB", "21.0ns avg<br>22MB", "19.9ns avg<br>37MB", "19.9ns avg<br>20MB", "17.3ns avg<br>37MB", "<b>17.1ns avg<br>0.0MB</b>", "17.0ns avg<br>23MB", "16.1ns avg<br>37MB", "15.5ns avg<br>23MB", "<b>14.8ns avg<br>9.5MB</b>", "<b>14.1ns avg<br>22MB</b>", "<b>14.0ns avg<br>22MB</b>", "<b>12.6ns avg<br>24MB</b>", "<b>12.5ns avg<br>25MB</b>" ],
        },
        { x: [ 6.27187e-08, 5.23211e-08, 3.51346e-08, 3.0023000000000004e-08, 3.10101e-08, 2.5042600000000003e-08, 2.25893e-08, 2.11475e-08, 1.454922e-08, 1.9029600000000004e-08, 1.297702e-08, 1.6913880000000003e-08, 1.805393e-08, 1.8041150000000004e-08, 9.78478e-09, 1.015228e-08, 1.2102810000000002e-08, 1.19858e-08, 1.096249e-08, 1.0940220000000001e-08 ],
          y: m2y, name: measurement_names[0] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[0], },
        },
        { x: [ 6.28498e-08, 5.23409e-08, 3.5225e-08, 3.0138e-08, 3.10544e-08, 2.5043600000000004e-08, 2.25447e-08, 2.1138700000000002e-08, 1.445252e-08, 1.899752e-08, 1.309245e-08, 1.688692e-08, 1.805959e-08, 1.8044350000000005e-08, 9.697220000000001e-09, 1.0049280000000001e-08, 1.222926e-08, 1.2072300000000002e-08, 1.1017500000000001e-08, 1.0938480000000001e-08 ],
          y: m2y, name: measurement_names[1] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[1], },
        },
        { x: [ 6.78287e-08, 5.62233e-08, 4.0268100000000006e-08, 3.75926e-08, 3.5305900000000006e-08, 3.136360000000001e-08, 2.98257e-08, 2.51754e-08, 2.0961200000000002e-08, 2.412e-08, 2.1214e-08, 2.3302200000000003e-08, 2.21634e-08, 2.09088e-08, 1.5655150000000002e-08, 1.621053e-08, 1.9062319999999998e-08, 1.90544e-08, 1.632625e-08, 1.6322780000000003e-08 ],
          y: m2y, name: measurement_names[2] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[2], },
        },
        { x: [ 6.78784e-08, 5.6167e-08, 4.0316800000000005e-08, 3.75708e-08, 3.5252099999999996e-08, 3.1335400000000004e-08, 2.98273e-08, 2.52486e-08, 2.08984e-08, 2.41383e-08, 2.10966e-08, 2.3342200000000002e-08, 2.22554e-08, 2.09137e-08, 1.582941e-08, 1.644075e-08, 1.910127e-08, 1.9030600000000002e-08, 1.633151e-08, 1.6316780000000002e-08 ],
          y: m2y, name: measurement_names[3] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[3], },
        },
        { x: [ 6.316790000000001e-08, 5.51666e-08, 3.9962e-08, 3.93388e-08, 3.46589e-08, 3.22298e-08, 3.0171100000000005e-08, 2.66705e-08, 2.4961400000000003e-08, 2.50311e-08, 2.45969e-08, 2.3644500000000004e-08, 2.3331300000000002e-08, 2.23424e-08, 1.9576580000000002e-08, 1.9363679999999998e-08, 1.9943110000000004e-08, 1.991124e-08, 1.7861950000000002e-08, 1.778858e-08 ],
          y: m2y, name: measurement_names[4] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[4], },
        },
        { x: [ 6.314650000000002e-08, 5.52395e-08, 3.99003e-08, 3.93272e-08, 3.46634e-08, 3.21988e-08, 3.01781e-08, 2.6690600000000002e-08, 2.49306e-08, 2.50015e-08, 2.45586e-08, 2.36579e-08, 2.33764e-08, 2.23837e-08, 1.96101e-08, 1.942219e-08, 1.998702e-08, 1.992206e-08, 1.7874040000000005e-08, 1.7813520000000002e-08 ],
          y: m2y, name: measurement_names[5] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[5], },
        },
        { x: [ 5.2925700000000004e-08, 5.0219e-08, 3.4921700000000004e-08, 3.6078600000000006e-08, 2.91742e-08, 2.9284800000000003e-08, 2.59442e-08, 2.46075e-08, 2.5523000000000003e-08, 2.1719400000000003e-08, 2.42621e-08, 2.01164e-08, 2.05867e-08, 2.0064e-08, 2.1311200000000002e-08, 2.1016100000000003e-08, 1.752581e-08, 1.736092e-08, 1.6455390000000003e-08, 1.629918e-08 ],
          y: m2y, name: measurement_names[6] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[6], },
        },
        { x: [ 5.288910000000001e-08, 5.011550000000001e-08, 3.4918e-08, 3.6093000000000004e-08, 2.91847e-08, 2.9319400000000004e-08, 2.5962200000000002e-08, 2.4736100000000003e-08, 2.5490800000000002e-08, 2.17061e-08, 2.41602e-08, 2.01038e-08, 2.0601400000000002e-08, 2.0091e-08, 2.1302500000000002e-08, 2.0977000000000003e-08, 1.7510730000000004e-08, 1.7334800000000002e-08, 1.6455430000000002e-08, 1.6393439999999998e-08 ],
          y: m2y, name: measurement_names[7] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[7], },
        },
        { x: [ 4.16203e-08, 4.1401800000000005e-08, 2.75503e-08, 2.97976e-08, 2.1529e-08, 2.41436e-08, 2.1050600000000003e-08, 2.13015e-08, 2.33832e-08, 1.703329e-08, 2.1272e-08, 1.632057e-08, 1.602022e-08, 1.5694790000000002e-08, 1.91039e-08, 1.831869e-08, 1.434743e-08, 1.427864e-08, 1.416544e-08, 1.4031000000000002e-08 ],
          y: m2y, name: measurement_names[8] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[8], },
        },
        { x: [ 4.17372e-08, 4.13522e-08, 2.7519600000000002e-08, 2.99324e-08, 2.14919e-08, 2.41116e-08, 2.1084800000000002e-08, 2.13266e-08, 2.3355800000000003e-08, 1.703167e-08, 2.12667e-08, 1.635358e-08, 1.608267e-08, 1.5696350000000003e-08, 1.9120990000000003e-08, 1.83043e-08, 1.4319490000000001e-08, 1.4283680000000001e-08, 1.4199400000000002e-08, 1.4039160000000002e-08 ],
          y: m2y, name: measurement_names[9] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[9], },
            textposition: 'outside',
            text: [ "57.7ns avg<br>15MB", "51.1ns avg<br>23MB", "35.6ns avg<br>11MB", "34.6ns avg<br>17MB", "30.3ns avg<br>27MB", "28.4ns avg<br>12MB", "25.9ns avg<br>0.0MB", "23.8ns avg<br>37MB", "21.9ns avg<br>22MB", "21.4ns avg<br>37MB", "20.8ns avg<br>20MB", "<b>20.1ns avg<br>0.0MB</b>", "20.1ns avg<br>23MB", "19.4ns avg<br>37MB", "17.1ns avg<br>23MB", "<b>17.0ns avg<br>9.5MB</b>", "<b>16.6ns avg<br>21MB</b>", "<b>16.5ns avg<br>23MB</b>", "<b>15.2ns avg<br>24MB</b>", "<b>15.1ns avg<br>25MB</b>" ],
        },
        { x: [ 7.001010000000001e-08, 6.207290000000001e-08, 3.95854e-08, 3.4957e-08, 3.84654e-08, 2.8594400000000003e-08, 2.65644e-08, 2.5018300000000002e-08, 1.918848e-08, 2.35023e-08, 1.7087659999999998e-08, 2.2610299999999998e-08, 2.07522e-08, 2.2293200000000004e-08, 1.509993e-08, 1.4983320000000002e-08, 1.2949350000000001e-08, 1.238423e-08, 1.367349e-08, 1.3651520000000001e-08 ],
          y: m3y, name: measurement_names[0] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[0], },
        },
        { x: [ 7.00754e-08, 6.21989e-08, 3.962420000000001e-08, 3.51898e-08, 3.85253e-08, 2.8669400000000002e-08, 2.6572200000000002e-08, 2.5190899999999998e-08, 1.913412e-08, 2.3517100000000004e-08, 1.719346e-08, 2.2579300000000003e-08, 2.0835e-08, 2.2268200000000002e-08, 1.5201250000000002e-08, 1.51377e-08, 1.289648e-08, 1.232224e-08, 1.373292e-08, 1.3748320000000001e-08 ],
          y: m3y, name: measurement_names[1] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[1], },
        },
        { x: [ 7.492910000000001e-08, 6.699810000000001e-08, 4.55254e-08, 4.32116e-08, 4.25914e-08, 3.5599800000000004e-08, 3.4236300000000006e-08, 3.0738e-08, 2.6385900000000003e-08, 2.95849e-08, 2.61523e-08, 2.70102e-08, 2.77229e-08, 2.66849e-08, 2.4254800000000004e-08, 2.382e-08, 1.974821e-08, 1.862175e-08, 2.03242e-08, 2.0246800000000002e-08 ],
          y: m3y, name: measurement_names[2] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[2], },
        },
        { x: [ 7.501189999999999e-08, 6.710370000000001e-08, 4.5528e-08, 4.31918e-08, 4.25489e-08, 3.5624e-08, 3.42401e-08, 3.0787e-08, 2.64369e-08, 2.9595700000000003e-08, 2.6238300000000003e-08, 2.70141e-08, 2.7769500000000003e-08, 2.6704600000000005e-08, 2.42874e-08, 2.38482e-08, 1.9524500000000004e-08, 1.8437900000000004e-08, 2.0353900000000003e-08, 2.0269400000000003e-08 ],
          y: m3y, name: measurement_names[3] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[3], },
        },
        { x: [ 7.152990000000001e-08, 6.499690000000002e-08, 4.55034e-08, 4.4517200000000005e-08, 4.13824e-08, 3.7298200000000005e-08, 3.44719e-08, 3.245750000000001e-08, 3.11476e-08, 3.0226900000000004e-08, 3.0175000000000004e-08, 2.7658000000000002e-08, 2.84162e-08, 2.78611e-08, 2.4778100000000004e-08, 2.4672400000000003e-08, 2.372e-08, 2.31745e-08, 2.2474e-08, 2.22964e-08 ],
          y: m3y, name: measurement_names[4] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[4], },
        },
        { x: [ 7.163380000000001e-08, 6.49778e-08, 4.5577e-08, 4.4564e-08, 4.13502e-08, 3.7382599999999996e-08, 3.44846e-08, 3.2413800000000004e-08, 3.11779e-08, 3.01793e-08, 3.0123600000000004e-08, 2.7662500000000004e-08, 2.8381e-08, 2.79569e-08, 2.4793000000000002e-08, 2.4632400000000004e-08, 2.37985e-08, 2.3166e-08, 2.2455100000000004e-08, 2.2308e-08 ],
          y: m3y, name: measurement_names[5] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[5], },
        },
        { x: [ 6.24654e-08, 5.9524500000000005e-08, 3.9897099999999995e-08, 4.10556e-08, 3.55093e-08, 3.38254e-08, 3.026660000000001e-08, 2.91193e-08, 3.15277e-08, 2.6959600000000003e-08, 2.9745700000000004e-08, 2.57277e-08, 2.49141e-08, 2.41439e-08, 2.22301e-08, 2.2046e-08, 2.5136200000000002e-08, 2.5166900000000004e-08, 2.0845300000000002e-08, 2.0816400000000004e-08 ],
          y: m3y, name: measurement_names[6] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[6], },
        },
        { x: [ 6.247730000000001e-08, 5.9591100000000006e-08, 3.97472e-08, 4.1084600000000005e-08, 3.54812e-08, 3.39764e-08, 3.02941e-08, 2.91685e-08, 3.15526e-08, 2.6922200000000002e-08, 2.9676300000000003e-08, 2.5719199999999998e-08, 2.4915500000000002e-08, 2.4200500000000002e-08, 2.21967e-08, 2.2048600000000003e-08, 2.50414e-08, 2.51256e-08, 2.0856800000000002e-08, 2.08336e-08 ],
          y: m3y, name: measurement_names[7] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[7], },
        },
        { x: [ 5.06207e-08, 4.9766699999999995e-08, 3.1780400000000006e-08, 3.4646000000000005e-08, 2.6631800000000002e-08, 2.8214e-08, 2.5343e-08, 2.4978900000000003e-08, 2.8791000000000003e-08, 2.21574e-08, 2.6261700000000004e-08, 2.04637e-08, 2.08107e-08, 1.9673630000000003e-08, 1.842935e-08, 1.831994e-08, 2.24499e-08, 2.32253e-08, 1.818925e-08, 1.798614e-08 ],
          y: m3y, name: measurement_names[8] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[8], },
        },
        { x: [ 5.0692e-08, 4.97732e-08, 3.16266e-08, 3.45954e-08, 2.6627100000000003e-08, 2.8277200000000003e-08, 2.5295900000000005e-08, 2.47543e-08, 2.8773500000000003e-08, 2.20756e-08, 2.60845e-08, 2.04405e-08, 2.0758e-08, 1.9804930000000002e-08, 1.8427950000000002e-08, 1.8316640000000002e-08, 2.2459500000000003e-08, 2.3269800000000002e-08, 1.819073e-08, 1.808904e-08 ],
          y: m3y, name: measurement_names[9] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[9], },
            textposition: 'outside',
            text: [ "65.9ns avg<br>20MB", "60.7ns avg<br>21MB", "40.4ns avg<br>11MB", "39.7ns avg<br>16MB", "36.9ns avg<br>27MB", "32.7ns avg<br>12MB", "30.2ns avg<br>0.0MB", "28.5ns avg<br>37MB", "27.4ns avg<br>22MB", "26.5ns avg<br>37MB", "25.9ns avg<br>20MB", "24.7ns avg<br>37MB", "<b>24.5ns avg<br>0.0MB</b>", "24.2ns avg<br>23MB", "21.0ns avg<br>22MB", "20.8ns avg<br>23MB", "<b>20.8ns avg<br>9.5MB</b>", "<b>20.5ns avg<br>23MB</b>", "<b>19.1ns avg<br>24MB</b>", "<b>19.0ns avg<br>25MB</b>" ],
        },
        { x: [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 5.333970000000001e-08, 4.48658e-08, 3.04358e-08, 2.3442800000000003e-08, 1.230374e-08, 1.092922e-08, 1.3412290000000001e-08 ],
          y: m4y, name: measurement_names[0] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[0], },
        },
        { x: [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 5.34479e-08, 4.5074900000000006e-08, 3.05427e-08, 2.3515300000000002e-08, 1.2305990000000001e-08, 1.1063020000000001e-08, 1.339651e-08 ],
          y: m4y, name: measurement_names[1] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[1], },
        },
        { x: [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 5.88813e-08, 4.87268e-08, 3.55718e-08, 2.7236600000000002e-08, 1.84678e-08, 1.8760390000000002e-08, 1.6794770000000002e-08 ],
          y: m4y, name: measurement_names[2] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[2], },
        },
        { x: [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 5.88515e-08, 4.86893e-08, 3.56213e-08, 2.71758e-08, 1.8478730000000003e-08, 1.880877e-08, 1.679864e-08 ],
          y: m4y, name: measurement_names[3] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[3], },
        },
        { x: [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 5.5973800000000005e-08, 4.77987e-08, 3.46392e-08, 2.7448000000000002e-08, 2.25259e-08, 2.22405e-08, 1.780558e-08 ],
          y: m4y, name: measurement_names[4] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[4], },
        },
        { x: [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 5.57367e-08, 4.78161e-08, 3.4572600000000006e-08, 2.7431400000000004e-08, 2.2547e-08, 2.23725e-08, 1.7851830000000002e-08 ],
          y: m4y, name: measurement_names[5] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[5], },
        },
        { x: [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 4.78063e-08, 4.2993800000000003e-08, 3.03283e-08, 2.3344300000000003e-08, 2.33882e-08, 2.21433e-08, 1.536894e-08 ],
          y: m4y, name: measurement_names[6] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[6], },
        },
        { x: [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 4.7763200000000006e-08, 4.29643e-08, 3.0414900000000006e-08, 2.3339500000000002e-08, 2.3312700000000002e-08, 2.2199800000000003e-08, 1.5405390000000003e-08 ],
          y: m4y, name: measurement_names[7] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[7], },
        },
        { x: [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 3.72382e-08, 3.48726e-08, 2.4466400000000004e-08, 1.675333e-08, 2.1378e-08, 1.9472519999999998e-08, 1.1949970000000001e-08 ],
          y: m4y, name: measurement_names[8] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[8], },
        },
        { x: [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 3.71699e-08, 3.486290000000001e-08, 2.4439e-08, 1.6754099999999998e-08, 2.13136e-08, 1.950713e-08, 1.1960650000000001e-08 ],
          y: m4y, name: measurement_names[9] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[9], },
            textposition: 'outside',
            text: [ "timeout", "timeout", "timeout", "timeout", "timeout", "timeout", "timeout", "timeout", "timeout", "timeout", "timeout", "timeout", "timeout", "50.6ns avg<br>22MB", "43.9ns avg<br>23MB", "<b>31.1ns avg<br>11MB</b>", "23.6ns avg<br>27MB", "19.6ns avg<br>22MB", "<b>18.7ns avg<br>20MB</b>", "<b>15.1ns avg<br>23MB</b>" ],
        },
    ];

    var layout = {
        // title: { text: 'RandomFind_500000'},
        grid: {
            ygap: 0.1,
            subplots: [
            ['xy'],
            ['xy2'],
            ['xy3'],
            ['xy4'],
            ['xy5'],
        ] },

        barmode: 'stack',
        yaxis: { title: 'robin_hood::hash', automargin: true, },
        yaxis2: { title: 'absl::Hash', automargin: true, },
        yaxis3: { title: 'folly::hasher', automargin: true, },
        yaxis4: { title: 'FNV1a', automargin: true, },
        yaxis5: { title: 'Identity', automargin: true, },
        xaxis: { automargin: true, },
        legend: { traceorder: 'normal' },
        margin: { pad: 0, l:0, r:0, t:0, b:0, },
        showlegend:false,
    };

    Plotly.newPlot('id_ac9de32b', data, layout);
</script>