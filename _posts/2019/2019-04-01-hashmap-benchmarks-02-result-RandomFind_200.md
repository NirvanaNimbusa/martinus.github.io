---
layout: post
title: Hashmaps Benchmarks - Find 1-200 Entries
subtitle: Finding the Fastest, Memory Efficient Hashmap
bigimg: /img/2019/X-15_in_flight_small.jpg
---

## Table of Contents

* [Overview](/2019/04/01/hashmap-benchmarks-01-overview/)
* Construction Benchmarks
   * [Construction & Destruction](/2019/04/01/hashmap-benchmarks-02-result-CtorDtorEmptyMap/)
   * [Construction & Insert 1 Element & Destruction](/2019/04/01/hashmap-benchmarks-02-result-CtorDtorSingleEntryMap/)
* Modifying Benchmarks
   * [Insert & Erase 100M Entries](/2019/04/01/hashmap-benchmarks-02-result-InsertHugeInt/)
   * [Insert or Access, Varying Probability](/2019/04/01/hashmap-benchmarks-02-result-RandomDistinct2/)
   * [Insert & Erase](/2019/04/01/hashmap-benchmarks-02-result-RandomInsertErase/)
   * [Insert & Erase Strings](/2019/04/01/hashmap-benchmarks-02-result-RandomInsertEraseStrings/)
* Accessing
   * **&rarr; [Find 1-200 Entries](/2019/04/01/hashmap-benchmarks-02-result-RandomFind_200/) &larr;**
   * [Find 1-2000 Entries](/2019/04/01/hashmap-benchmarks-02-result-RandomFind_2000/)
   * [Find 1-500k Entries](/2019/04/01/hashmap-benchmarks-02-result-RandomFind_500000/)
   * [Iterating](/2019/04/01/hashmap-benchmarks-02-result-IterateIntegers/)
* [Conclusion](/2019/04/01/hashmap-benchmarks-03-conclusion/)

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
<div id="id_54e74987" style="height:250em"></div>
<script>
    var colors = Plotly.d3.scale.category10().range();
    var m0y = [ "std::unordered_map", "boost::unordered_map", "eastl::hash_map", "folly::F14NodeMap", "folly::F14ValueMap", "spp::sparse_hash_map", "phmap::<br>parallel_flat_hash_map", "phmap::<br>parallel_node_hash_map", "robin_hood::<br>unordered_flat_map", "tsl::sparse_map", "robin_hood::<br>unordered_node_map", "phmap::flat_hash_map", "absl::flat_hash_map", "absl::node_hash_map", "phmap::node_hash_map", "ska::bytell_hash_map", "tsl::hopscotch_map", "boost::multi_index::<br>hashed_unique", "tsl::robin_map", "<b>emilib1::HashMap</b>"];
    var m1y = [ "std::unordered_map", "boost::unordered_map", "eastl::hash_map", "folly::F14NodeMap", "folly::F14ValueMap", "spp::sparse_hash_map", "phmap::<br>parallel_node_hash_map", "phmap::<br>parallel_flat_hash_map", "robin_hood::<br>unordered_node_map", "robin_hood::<br>unordered_flat_map", "phmap::flat_hash_map", "absl::flat_hash_map", "absl::node_hash_map", "tsl::sparse_map", "phmap::node_hash_map", "ska::bytell_hash_map", "tsl::hopscotch_map", "boost::multi_index::<br>hashed_unique", "emilib1::HashMap", "<b>tsl::robin_map</b>"];
    var m2y = [ "std::unordered_map", "boost::unordered_map", "eastl::hash_map", "folly::F14ValueMap", "folly::F14NodeMap", "spp::sparse_hash_map", "phmap::<br>parallel_flat_hash_map", "phmap::<br>parallel_node_hash_map", "phmap::flat_hash_map", "absl::flat_hash_map", "phmap::node_hash_map", "absl::node_hash_map", "tsl::sparse_map", "ska::bytell_hash_map", "robin_hood::<br>unordered_flat_map", "robin_hood::<br>unordered_node_map", "boost::multi_index::<br>hashed_unique", "tsl::hopscotch_map", "emilib1::HashMap", "<b>tsl::robin_map</b>"];
    var m3y = [ "std::unordered_map", "folly::F14ValueMap", "folly::F14NodeMap", "boost::unordered_map", "eastl::hash_map", "spp::sparse_hash_map", "phmap::<br>parallel_flat_hash_map", "phmap::<br>parallel_node_hash_map", "tsl::sparse_map", "phmap::node_hash_map", "absl::node_hash_map", "absl::flat_hash_map", "phmap::flat_hash_map", "ska::bytell_hash_map", "robin_hood::<br>unordered_node_map", "robin_hood::<br>unordered_flat_map", "boost::multi_index::<br>hashed_unique", "tsl::hopscotch_map", "emilib1::HashMap", "<b>tsl::robin_map</b>"];
    var m4y = [ "spp::sparse_hash_map", "robin_hood::<br>unordered_node_map", "robin_hood::<br>unordered_flat_map", "emilib1::HashMap", "tsl::sparse_map", "tsl::robin_map", "phmap::<br>parallel_flat_hash_map", "phmap::flat_hash_map", "absl::flat_hash_map", "phmap::<br>parallel_node_hash_map", "phmap::node_hash_map", "absl::node_hash_map", "tsl::hopscotch_map", "std::unordered_map", "boost::unordered_map", "eastl::hash_map", "folly::F14NodeMap", "folly::F14ValueMap", "boost::multi_index::<br>hashed_unique", "<b>ska::bytell_hash_map</b>"];
    var measurement_names = [ "0% success, 0x00000000ffffffff", "0% success, 0xffffffff00000000", "25% success, 0x00000000ffffffff", "25% success, 0xffffffff00000000", "50% success, 0x00000000ffffffff", "50% success, 0xffffffff00000000", "75% success, 0x00000000ffffffff", "75% success, 0xffffffff00000000", "100% success, 0x00000000ffffffff", "100% success, 0xffffffff00000000" ];

    var data = [
        { x: [ 1.57525e-08, 1.0776099999999999e-08, 9.717605000000001e-09, 5.988055e-09, 6.059755000000001e-09, 8.80018e-09, 5.46702e-09, 5.23682e-09, 4.703215e-09, 4.16854e-09, 4.665635e-09, 3.694245e-09, 3.628215e-09, 3.751605e-09, 3.74728e-09, 3.2460150000000004e-09, 3.344435e-09, 3.5085100000000003e-09, 2.909805e-09, 2.46405e-09 ],
          y: m0y, name: measurement_names[0] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[0], },
        },
        { x: [ 1.5544600000000003e-08, 1.07269e-08, 9.693330000000002e-09, 5.935580000000001e-09, 5.99056e-09, 8.978805e-09, 5.415535e-09, 5.293095e-09, 4.61866e-09, 4.195985e-09, 4.639655000000001e-09, 3.58393e-09, 3.5660600000000004e-09, 3.7502700000000005e-09, 3.6996150000000006e-09, 3.21719e-09, 3.3445200000000004e-09, 3.45474e-09, 2.8647900000000004e-09, 2.4264300000000003e-09 ],
          y: m0y, name: measurement_names[1] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[1], },
        },
        { x: [ 1.4309900000000002e-08, 1.077785e-08, 9.874715000000001e-09, 7.608145000000001e-09, 7.462230000000001e-09, 8.159805e-09, 5.672220000000001e-09, 5.49352e-09, 4.1177e-09, 3.899795e-09, 4.066715000000001e-09, 3.6495000000000003e-09, 3.5963750000000004e-09, 3.61525e-09, 3.64285e-09, 3.3402200000000003e-09, 3.4867450000000002e-09, 3.518505e-09, 2.942205e-09, 2.5947e-09 ],
          y: m0y, name: measurement_names[2] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[2], },
        },
        { x: [ 1.4812150000000002e-08, 1.07434e-08, 9.920045e-09, 7.3867050000000005e-09, 7.312990000000001e-09, 8.171750000000002e-09, 5.655155e-09, 5.55834e-09, 4.103775000000001e-09, 3.97236e-09, 4.035875e-09, 3.6253050000000002e-09, 3.5997750000000002e-09, 3.624575e-09, 3.580915e-09, 3.6778950000000004e-09, 3.302305e-09, 3.5135750000000002e-09, 2.9587800000000004e-09, 2.552985e-09 ],
          y: m0y, name: measurement_names[3] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[3], },
        },
        { x: [ 1.35161e-08, 1.0429350000000001e-08, 9.85559e-09, 8.973390000000002e-09, 8.77233e-09, 7.158380000000001e-09, 5.6365650000000006e-09, 5.56176e-09, 3.88989e-09, 3.8388650000000005e-09, 3.6565600000000005e-09, 3.84739e-09, 3.79883e-09, 3.76577e-09, 3.768155e-09, 3.589e-09, 3.6864450000000003e-09, 3.49424e-09, 2.99453e-09, 2.801325e-09 ],
          y: m0y, name: measurement_names[4] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[4], },
        },
        { x: [ 1.3693500000000001e-08, 1.039645e-08, 9.828190000000001e-09, 8.933825e-09, 8.7146e-09, 7.215305000000001e-09, 5.787925e-09, 5.703385e-09, 3.8909500000000005e-09, 3.936245e-09, 3.6311950000000005e-09, 3.791575e-09, 3.7908750000000005e-09, 3.7199950000000004e-09, 3.73757e-09, 3.8677849999999996e-09, 3.765420000000001e-09, 3.4927250000000003e-09, 3.0883800000000005e-09, 2.8207700000000004e-09 ],
          y: m0y, name: measurement_names[5] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[5], },
        },
        { x: [ 1.2977250000000001e-08, 1.07862e-08, 9.97345e-09, 1.09125e-08, 1.061355e-08, 7.0221e-09, 5.758140000000001e-09, 5.7021950000000005e-09, 3.733645e-09, 3.7723e-09, 3.6309400000000003e-09, 3.993685e-09, 3.9847e-09, 3.8483650000000005e-09, 3.8521e-09, 3.845995e-09, 3.81311e-09, 3.548995e-09, 2.984065e-09, 2.78922e-09 ],
          y: m0y, name: measurement_names[6] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[6], },
        },
        { x: [ 1.33058e-08, 1.070545e-08, 9.935375e-09, 1.1093650000000001e-08, 1.0877300000000002e-08, 7.26097e-09, 5.993280000000001e-09, 5.840590000000001e-09, 3.910695e-09, 4.00384e-09, 3.781235e-09, 3.945155e-09, 3.96862e-09, 3.829645e-09, 3.842260000000001e-09, 4.042755e-09, 4.304285e-09, 3.50039e-09, 3.1926200000000005e-09, 2.86456e-09 ],
          y: m0y, name: measurement_names[7] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[7], },
        },
        { x: [ 1.26227e-08, 1.07665e-08, 9.909599999999999e-09, 1.3118900000000001e-08, 1.27434e-08, 6.9124e-09, 6.06476e-09, 5.996830000000001e-09, 3.655535e-09, 4.172435000000001e-09, 3.4807250000000003e-09, 4.31642e-09, 4.268345e-09, 4.14813e-09, 4.118020000000001e-09, 4.05983e-09, 3.916485e-09, 3.68379e-09, 3.1509150000000006e-09, 2.9910550000000003e-09 ],
          y: m0y, name: measurement_names[8] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[8], },
        },
        { x: [ 1.28248e-08, 1.07025e-08, 9.901185e-09, 1.2731150000000001e-08, 1.2287599999999999e-08, 7.373455e-09, 6.130755e-09, 5.989010000000001e-09, 3.75387e-09, 4.28732e-09, 3.5558500000000003e-09, 4.3073800000000005e-09, 4.2864550000000006e-09, 4.147065e-09, 4.1159850000000004e-09, 4.2443e-09, 3.95059e-09, 3.6605950000000004e-09, 3.217465e-09, 3.0181e-09 ],
          y: m0y, name: measurement_names[9] + ' (robin_hood::hash)', type: 'bar', orientation: 'h', yaxis: 'y', marker: { color: colors[9], },
            textposition: 'outside',
            text: [ "13.9ns avg<br>0.0MB", "10.7ns avg<br>0.0MB", "9.86ns avg<br>0.0MB", "9.27ns avg<br>0.0MB", "9.08ns avg<br>0.0MB", "7.71ns avg<br>0.0MB", "5.76ns avg<br>0.0MB", "5.64ns avg<br>0.0MB", "4.04ns avg<br>0.0MB", "4.02ns avg<br>0.0MB", "3.91ns avg<br>0.0MB", "3.88ns avg<br>0.0MB", "3.85ns avg<br>0.0MB", "3.82ns avg<br>0.0MB", "3.81ns avg<br>0.0MB", "3.71ns avg<br>0.0MB", "3.69ns avg<br>0.0MB", "3.54ns avg<br>0.0MB", "3.03ns avg<br>0.0MB", "<b>2.73ns avg<br>0.0MB</b>" ],
        },
        { x: [ 1.576165e-08, 1.10161e-08, 9.70536e-09, 6.093965e-09, 6.130420000000001e-09, 9.054430000000001e-09, 5.68633e-09, 5.693345000000001e-09, 5.024290000000001e-09, 5.126005000000001e-09, 3.97984e-09, 4.072225000000001e-09, 4.062295e-09, 4.234015e-09, 3.988105e-09, 3.5389550000000004e-09, 3.3519700000000004e-09, 3.572715e-09, 2.6224650000000004e-09, 2.522435e-09 ],
          y: m1y, name: measurement_names[0] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[0], },
        },
        { x: [ 1.5759500000000003e-08, 1.103275e-08, 9.683625e-09, 6.0454800000000005e-09, 6.1584750000000005e-09, 8.983185000000001e-09, 5.6585150000000005e-09, 5.721175e-09, 5.033375e-09, 5.1374399999999996e-09, 3.941215e-09, 4.085345e-09, 4.1248099999999995e-09, 4.30033e-09, 3.93873e-09, 3.498765e-09, 3.25704e-09, 3.56845e-09, 2.7003e-09, 2.5606650000000005e-09 ],
          y: m1y, name: measurement_names[1] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[1], },
        },
        { x: [ 1.4320900000000001e-08, 1.097865e-08, 1.0017800000000002e-08, 7.572835000000001e-09, 7.51758e-09, 8.190365e-09, 5.854405000000001e-09, 5.88014e-09, 4.71632e-09, 4.64703e-09, 4.016505e-09, 4.040665e-09, 3.98962e-09, 4.175075e-09, 4.042125e-09, 3.6648850000000002e-09, 3.647845e-09, 3.55802e-09, 2.8216600000000004e-09, 2.59074e-09 ],
          y: m1y, name: measurement_names[2] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[2], },
        },
        { x: [ 1.4165050000000001e-08, 1.0961450000000001e-08, 1.0014400000000001e-08, 7.503970000000001e-09, 7.36909e-09, 8.349250000000001e-09, 5.965925e-09, 5.961180000000001e-09, 4.7327600000000005e-09, 4.616955e-09, 4.05749e-09, 4.06679e-09, 4.017875000000001e-09, 4.453484999999999e-09, 3.98599e-09, 3.685445e-09, 3.671765e-09, 3.566835e-09, 2.810035e-09, 2.65914e-09 ],
          y: m1y, name: measurement_names[3] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[3], },
        },
        { x: [ 1.364805e-08, 1.09379e-08, 1.01203e-08, 9.227125e-09, 9.080035e-09, 7.17557e-09, 5.9497100000000005e-09, 6.012845e-09, 4.495555000000001e-09, 4.2028000000000004e-09, 4.14397e-09, 4.1453000000000006e-09, 4.171835e-09, 3.995995e-09, 4.154765000000001e-09, 3.86214e-09, 3.528435e-09, 3.6380500000000003e-09, 2.98221e-09, 2.6526650000000006e-09 ],
          y: m1y, name: measurement_names[4] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[4], },
        },
        { x: [ 1.37369e-08, 1.09193e-08, 1.01107e-08, 9.25167e-09, 9.040340000000001e-09, 7.48378e-09, 5.9942150000000005e-09, 6.1000300000000005e-09, 4.4939850000000006e-09, 4.210970000000001e-09, 4.2868700000000005e-09, 4.1878900000000005e-09, 4.224645000000001e-09, 4.261695e-09, 4.16648e-09, 3.93671e-09, 3.8886200000000006e-09, 3.6314750000000004e-09, 3.04839e-09, 2.7395750000000002e-09 ],
          y: m1y, name: measurement_names[5] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[5], },
        },
        { x: [ 1.403125e-08, 1.074615e-08, 1.009575e-08, 1.1196650000000002e-08, 1.09381e-08, 7.14203e-09, 5.99608e-09, 6.145705e-09, 4.376045e-09, 4.273225e-09, 4.3079950000000005e-09, 4.31196e-09, 4.37429e-09, 4.031445000000001e-09, 4.2656299999999995e-09, 4.1100950000000006e-09, 4.23907e-09, 3.66418e-09, 3.10034e-09, 2.73311e-09 ],
          y: m1y, name: measurement_names[6] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[6], },
        },
        { x: [ 1.4095250000000002e-08, 1.074465e-08, 1.00957e-08, 1.12327e-08, 1.0997400000000001e-08, 7.071710000000001e-09, 6.061915000000001e-09, 6.192195000000001e-09, 4.31509e-09, 4.1628000000000006e-09, 4.3451150000000005e-09, 4.276405e-09, 4.30986e-09, 4.1703950000000005e-09, 4.21708e-09, 4.129265e-09, 4.8517750000000004e-09, 3.6387750000000006e-09, 3.138065e-09, 2.8072e-09 ],
          y: m1y, name: measurement_names[7] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[7], },
        },
        { x: [ 1.271305e-08, 1.0829150000000002e-08, 1.0166050000000001e-08, 1.3192350000000002e-08, 1.276295e-08, 7.82274e-09, 7.0411699999999996e-09, 6.448555e-09, 4.1416850000000005e-09, 4.03437e-09, 4.65389e-09, 4.617345e-09, 4.4283050000000005e-09, 4.168055000000001e-09, 4.49606e-09, 4.367920000000001e-09, 3.993365e-09, 3.872515e-09, 3.31224e-09, 2.853205e-09 ],
          y: m1y, name: measurement_names[8] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[8], },
        },
        { x: [ 1.2683000000000002e-08, 1.0824250000000002e-08, 1.0163700000000001e-08, 1.30875e-08, 1.2734700000000002e-08, 7.990640000000001e-09, 7.060615000000001e-09, 6.379935000000001e-09, 4.1724400000000005e-09, 4.064735e-09, 4.6843700000000004e-09, 4.60168e-09, 4.434445e-09, 4.31676e-09, 4.477915e-09, 4.46123e-09, 4.3899550000000005e-09, 3.9024400000000004e-09, 3.3312300000000007e-09, 2.8923950000000005e-09 ],
          y: m1y, name: measurement_names[9] + ' (absl::Hash)', type: 'bar', orientation: 'h', yaxis: 'y2', marker: { color: colors[9], },
            textposition: 'outside',
            text: [ "14.1ns avg<br>0.0MB", "10.9ns avg<br>0.0MB", "10.0ns avg<br>0.0MB", "9.44ns avg<br>0.0MB", "9.27ns avg<br>0.0MB", "7.93ns avg<br>0.0MB", "6.13ns avg<br>0.0MB", "6.05ns avg<br>0.0MB", "4.55ns avg<br>0.0MB", "4.45ns avg<br>0.0MB", "4.24ns avg<br>0.0MB", "4.24ns avg<br>0.0MB", "4.21ns avg<br>0.0MB", "4.21ns avg<br>0.0MB", "4.17ns avg<br>0.0MB", "3.93ns avg<br>0.0MB", "3.88ns avg<br>0.0MB", "3.66ns avg<br>0.0MB", "2.99ns avg<br>0.0MB", "<b>2.70ns avg<br>0.0MB</b>" ],
        },
        { x: [ 1.9417e-08, 1.1494650000000002e-08, 1.0255550000000001e-08, 6.844315e-09, 6.822420000000001e-09, 1.0048200000000001e-08, 8.125185000000001e-09, 7.661005e-09, 5.575315e-09, 5.515065e-09, 5.763185e-09, 5.713785e-09, 5.5185900000000005e-09, 4.718685e-09, 5.755975000000001e-09, 5.65265e-09, 4.7938050000000005e-09, 4.738795e-09, 3.731245000000001e-09, 3.83332e-09 ],
          y: m2y, name: measurement_names[0] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[0], },
        },
        { x: [ 1.897925e-08, 1.147105e-08, 1.02514e-08, 6.9300900000000004e-09, 6.9112150000000005e-09, 9.882840000000002e-09, 8.39696e-09, 7.762125e-09, 5.532560000000001e-09, 5.50447e-09, 5.7104600000000006e-09, 5.67686e-09, 5.629555e-09, 4.79519e-09, 5.826225e-09, 5.70299e-09, 4.789965000000001e-09, 4.788995000000001e-09, 3.766695e-09, 3.832395e-09 ],
          y: m2y, name: measurement_names[1] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[1], },
        },
        { x: [ 1.8313e-08, 1.1452100000000001e-08, 1.051115e-08, 8.371765000000002e-09, 8.316805e-09, 8.807725e-09, 7.95834e-09, 7.93459e-09, 5.737575e-09, 5.6615250000000005e-09, 5.795455e-09, 5.60591e-09, 5.3260250000000005e-09, 4.955155e-09, 5.210185e-09, 5.20207e-09, 4.863265e-09, 4.6678850000000006e-09, 3.89045e-09, 3.919640000000001e-09 ],
          y: m2y, name: measurement_names[2] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[2], },
        },
        { x: [ 1.78995e-08, 1.1494900000000003e-08, 1.0507400000000001e-08, 8.191415e-09, 8.118175e-09, 8.816815000000001e-09, 8.076445000000002e-09, 7.979935e-09, 5.711065e-09, 5.65012e-09, 5.78089e-09, 5.60212e-09, 5.5017100000000004e-09, 4.958935e-09, 5.2970400000000006e-09, 5.18132e-09, 4.9098750000000005e-09, 4.873425e-09, 3.868755000000001e-09, 3.974555e-09 ],
          y: m2y, name: measurement_names[3] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[3], },
        },
        { x: [ 1.76105e-08, 1.142105e-08, 1.0273e-08, 1.0024750000000002e-08, 9.998990000000001e-09, 8.559655000000001e-09, 8.269140000000001e-09, 8.148545e-09, 5.8607200000000004e-09, 5.863e-09, 5.905930000000001e-09, 5.71144e-09, 5.481215e-09, 5.307765000000001e-09, 5.108445e-09, 4.954535e-09, 4.93468e-09, 4.7882050000000004e-09, 4.101455000000001e-09, 4.02666e-09 ],
          y: m2y, name: measurement_names[4] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[4], },
        },
        { x: [ 1.7612450000000004e-08, 1.1481450000000002e-08, 1.029455e-08, 9.830230000000002e-09, 9.833345e-09, 8.463294999999999e-09, 8.25529e-09, 8.166190000000001e-09, 5.9346700000000006e-09, 5.947650000000001e-09, 5.948805000000001e-09, 5.795935e-09, 5.4095e-09, 5.3202349999999995e-09, 5.167700000000001e-09, 5.009530000000001e-09, 5.01005e-09, 4.754795000000001e-09, 4.02812e-09, 4.0300500000000006e-09 ],
          y: m2y, name: measurement_names[5] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[5], },
        },
        { x: [ 1.634085e-08, 1.1397049999999999e-08, 1.05655e-08, 1.188895e-08, 1.18695e-08, 8.17908e-09, 8.451120000000001e-09, 8.127255e-09, 6.044985000000001e-09, 6.041090000000001e-09, 5.839165e-09, 5.833045e-09, 5.428785e-09, 5.80333e-09, 4.917230000000001e-09, 4.844495000000001e-09, 5.020330000000001e-09, 4.95997e-09, 4.30048e-09, 4.027075e-09 ],
          y: m2y, name: measurement_names[6] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[6], },
        },
        { x: [ 1.6653350000000003e-08, 1.145025e-08, 1.05903e-08, 1.2104100000000001e-08, 1.214095e-08, 8.285505e-09, 8.505165e-09, 8.22476e-09, 6.08631e-09, 6.1037850000000005e-09, 5.873135000000001e-09, 5.88222e-09, 5.55066e-09, 5.493170000000001e-09, 5.0988150000000005e-09, 5.023155e-09, 5.0449450000000005e-09, 4.913940000000001e-09, 4.34583e-09, 4.088205000000001e-09 ],
          y: m2y, name: measurement_names[7] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[7], },
        },
        { x: [ 1.567195e-08, 1.13638e-08, 1.0645e-08, 1.4372550000000001e-08, 1.4390300000000001e-08, 8.665985000000001e-09, 8.645215000000001e-09, 8.380635e-09, 6.41142e-09, 6.4119350000000006e-09, 6.2210950000000004e-09, 6.185080000000001e-09, 5.957730000000001e-09, 5.926575000000001e-09, 4.92042e-09, 4.848595000000001e-09, 5.199255000000001e-09, 5.510555000000001e-09, 4.611515e-09, 4.13278e-09 ],
          y: m2y, name: measurement_names[8] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[8], },
        },
        { x: [ 1.59698e-08, 1.142915e-08, 1.0666800000000001e-08, 1.400305e-08, 1.399695e-08, 8.619365e-09, 8.651445e-09, 8.34081e-09, 6.479565000000001e-09, 6.451775e-09, 6.131460000000001e-09, 6.1967500000000005e-09, 5.72732e-09, 5.8388350000000005e-09, 5.1399650000000005e-09, 5.057975e-09, 5.2331200000000015e-09, 5.232425e-09, 4.581825e-09, 4.189645000000001e-09 ],
          y: m2y, name: measurement_names[9] + ' (folly::hasher)', type: 'bar', orientation: 'h', yaxis: 'y3', marker: { color: colors[9], },
            textposition: 'outside',
            text: [ "17.4ns avg<br>0.0MB", "11.4ns avg<br>0.0MB", "10.5ns avg<br>0.0MB", "10.3ns avg<br>0.0MB", "10.2ns avg<br>0.0MB", "8.83ns avg<br>0.0MB", "8.33ns avg<br>0.0MB", "8.07ns avg<br>0.0MB", "5.94ns avg<br>0.0MB", "5.92ns avg<br>0.0MB", "5.90ns avg<br>0.0MB", "5.82ns avg<br>0.0MB", "5.55ns avg<br>0.0MB", "5.31ns avg<br>0.0MB", "5.24ns avg<br>0.0MB", "5.15ns avg<br>0.0MB", "4.98ns avg<br>0.0MB", "4.92ns avg<br>0.0MB", "4.12ns avg<br>0.0MB", "<b>4.01ns avg<br>0.0MB</b>" ],
        },
        { x: [ 1.9071e-08, 1.0194600000000001e-08, 1.018315e-08, 1.329285e-08, 1.2021900000000001e-08, 1.226175e-08, 1.0098650000000001e-08, 1.00398e-08, 7.882370000000001e-09, 7.693435000000001e-09, 7.667855e-09, 7.630995e-09, 7.5096e-09, 6.467005e-09, 7.810830000000002e-09, 7.92865e-09, 6.8871550000000005e-09, 6.401645000000001e-09, 5.962365e-09, 5.7545e-09 ],
          y: m3y, name: measurement_names[0] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[0], },
        },
        { x: [ 1.8492200000000002e-08, 1.0232750000000001e-08, 1.0221400000000001e-08, 1.3211550000000002e-08, 1.20098e-08, 1.245635e-08, 1.05088e-08, 1.0414000000000001e-08, 8.2457e-09, 7.69199e-09, 7.721070000000001e-09, 7.642005000000001e-09, 7.618940000000001e-09, 6.804135000000001e-09, 7.815425e-09, 7.915865e-09, 6.854565e-09, 6.46911e-09, 6.1748850000000004e-09, 5.8575650000000005e-09 ],
          y: m3y, name: measurement_names[1] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[1], },
        },
        { x: [ 1.8570150000000002e-08, 1.1628650000000002e-08, 1.156525e-08, 1.30349e-08, 1.22906e-08, 1.152065e-08, 1.04783e-08, 1.0478899999999999e-08, 8.161855e-09, 7.70269e-09, 7.712635e-09, 7.628365000000001e-09, 7.552409999999999e-09, 6.897375000000001e-09, 7.2313e-09, 7.24755e-09, 6.938885e-09, 6.768670000000001e-09, 6.202485000000001e-09, 5.87881e-09 ],
          y: m3y, name: measurement_names[2] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[2], },
        },
        { x: [ 1.765395e-08, 1.16724e-08, 1.157355e-08, 1.3016500000000001e-08, 1.2274500000000002e-08, 1.159735e-08, 1.0614e-08, 1.05612e-08, 8.190444999999999e-09, 7.833565e-09, 7.839735000000001e-09, 7.834825e-09, 7.706045e-09, 6.7834e-09, 7.093775e-09, 7.08752e-09, 6.9269450000000004e-09, 6.756265e-09, 6.2549e-09, 5.905735e-09 ],
          y: m3y, name: measurement_names[3] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[3], },
        },
        { x: [ 1.75928e-08, 1.3551750000000001e-08, 1.3336550000000001e-08, 1.3112500000000002e-08, 1.2323100000000001e-08, 1.0887750000000001e-08, 1.076755e-08, 1.05855e-08, 7.850530000000001e-09, 7.934750000000001e-09, 7.868455e-09, 7.82395e-09, 7.80962e-09, 7.13908e-09, 7.058695e-09, 7.01474e-09, 7.043605e-09, 6.744105000000001e-09, 6.29921e-09, 5.8927050000000005e-09 ],
          y: m3y, name: measurement_names[4] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[4], },
        },
        { x: [ 1.71947e-08, 1.3579500000000001e-08, 1.3385350000000001e-08, 1.3264e-08, 1.23058e-08, 1.108985e-08, 1.08952e-08, 1.0655600000000001e-08, 8.00781e-09, 7.920655000000001e-09, 7.879495e-09, 7.87822e-09, 7.835695e-09, 7.2830050000000005e-09, 7.346845000000001e-09, 7.30888e-09, 7.1377550000000004e-09, 6.721440000000001e-09, 6.34883e-09, 5.966435000000001e-09 ],
          y: m3y, name: measurement_names[5] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[5], },
        },
        { x: [ 1.63406e-08, 1.56413e-08, 1.547965e-08, 1.307575e-08, 1.21642e-08, 1.079375e-08, 1.0968849999999999e-08, 1.086545e-08, 7.72707e-09, 8.092935000000001e-09, 8.084375e-09, 8.034655e-09, 7.95694e-09, 7.712450000000001e-09, 6.94111e-09, 6.862995e-09, 7.1060350000000005e-09, 6.746115e-09, 6.335155e-09, 5.915825e-09 ],
          y: m3y, name: measurement_names[6] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[6], },
        },
        { x: [ 1.62202e-08, 1.574815e-08, 1.5560150000000003e-08, 1.3206550000000001e-08, 1.2158600000000001e-08, 1.0906200000000001e-08, 1.110135e-08, 1.100315e-08, 7.972415e-09, 8.093030000000001e-09, 8.088865e-09, 8.065555000000001e-09, 7.969865000000001e-09, 7.349060000000001e-09, 6.9875650000000006e-09, 6.90406e-09, 7.191725000000001e-09, 6.987615000000001e-09, 6.5005300000000005e-09, 6.089200000000001e-09 ],
          y: m3y, name: measurement_names[7] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[7], },
        },
        { x: [ 1.56902e-08, 1.74609e-08, 1.7270000000000002e-08, 1.3049600000000001e-08, 1.2220400000000001e-08, 1.1474500000000001e-08, 1.2230000000000001e-08, 1.1064800000000001e-08, 8.32105e-09, 8.46936e-09, 8.448845e-09, 8.283365e-09, 8.31624e-09, 7.990355e-09, 6.94312e-09, 6.853500000000001e-09, 7.378325e-09, 7.096450000000001e-09, 6.58873e-09, 6.069105e-09 ],
          y: m3y, name: measurement_names[8] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[8], },
        },
        { x: [ 1.558455e-08, 1.7446300000000003e-08, 1.725335e-08, 1.3203650000000001e-08, 1.22413e-08, 1.1378000000000001e-08, 1.2268500000000002e-08, 1.11514e-08, 7.989670000000001e-09, 8.516990000000001e-09, 8.449945e-09, 8.332030000000001e-09, 8.243349999999999e-09, 7.817135e-09, 6.928430000000001e-09, 6.839875e-09, 7.398850000000001e-09, 7.020700000000001e-09, 6.6368400000000004e-09, 6.14777e-09 ],
          y: m3y, name: measurement_names[9] + ' (FNV1a)', type: 'bar', orientation: 'h', yaxis: 'y4', marker: { color: colors[9], },
            textposition: 'outside',
            text: [ "17.2ns avg<br>0.0MB", "13.7ns avg<br>0.0MB", "13.6ns avg<br>0.0MB", "13.1ns avg<br>0.0MB", "12.2ns avg<br>0.0MB", "11.4ns avg<br>0.0MB", "11.0ns avg<br>0.0MB", "10.7ns avg<br>0.0MB", "8.03ns avg<br>0.0MB", "7.99ns avg<br>0.0MB", "7.98ns avg<br>0.0MB", "7.92ns avg<br>0.0MB", "7.85ns avg<br>0.0MB", "7.22ns avg<br>0.0MB", "7.22ns avg<br>0.0MB", "7.20ns avg<br>0.0MB", "7.09ns avg<br>0.0MB", "6.77ns avg<br>0.0MB", "6.33ns avg<br>0.0MB", "<b>5.95ns avg<br>0.0MB</b>" ],
        },
        { x: [ 0, 0, 0, 0, 4.175510000000001e-09, 2.546975e-09, 4.793620000000001e-09, 3.37136e-09, 3.2374750000000003e-09, 4.700035e-09, 3.4628850000000007e-09, 3.4218750000000002e-09, 3.12597e-09, 1.4594000000000002e-08, 1.0521900000000001e-08, 9.33298e-09, 5.24818e-09, 5.2889050000000006e-09, 3.10817e-09, 2.619385e-09 ],
          y: m4y, name: measurement_names[0] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[0], },
        },
        { x: [ 0, 0, 0, 0, 1.8246700000000002e-07, 1.722815e-07, 1.5318300000000003e-07, 1.4479500000000002e-07, 1.4431350000000002e-07, 1.2785750000000002e-07, 1.2750450000000002e-07, 1.2711350000000002e-07, 9.431265e-08, 1.4690700000000001e-08, 1.06489e-08, 9.36002e-09, 5.291275e-09, 5.331470000000001e-09, 3.12881e-09, 2.8753200000000004e-09 ],
          y: m4y, name: measurement_names[1] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[1], },
        },
        { x: [ 0, 0, 0, 0, 3.68903e-09, 2.43274e-09, 5.000945e-09, 3.2581800000000004e-09, 3.2928150000000003e-09, 4.756495e-09, 3.2318000000000004e-09, 3.36205e-09, 3.20171e-09, 1.4069750000000001e-08, 1.031835e-08, 9.446325000000002e-09, 6.6181650000000006e-09, 6.676995000000001e-09, 3.20928e-09, 3.0955000000000003e-09 ],
          y: m4y, name: measurement_names[2] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[2], },
        },
        { x: [ 0, 0, 0, 0, 1.6170600000000003e-07, 1.4853550000000001e-07, 1.374865e-07, 1.292645e-07, 1.28788e-07, 1.299455e-07, 1.147925e-07, 1.1702950000000001e-07, 8.395435000000001e-08, 1.4062650000000001e-08, 1.04082e-08, 9.429665000000001e-09, 6.6752550000000004e-09, 6.7671900000000005e-09, 3.2197800000000002e-09, 2.864305e-09 ],
          y: m4y, name: measurement_names[3] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[3], },
        },
        { x: [ 0, 0, 0, 0, 3.675265e-09, 2.48174e-09, 5.014375000000001e-09, 3.3604550000000003e-09, 3.3302650000000004e-09, 4.925930000000001e-09, 3.27895e-09, 3.3419600000000003e-09, 3.7314e-09, 1.3604450000000002e-08, 1.0307450000000002e-08, 9.45064e-09, 8.39955e-09, 8.249725e-09, 3.3828000000000004e-09, 3.260125e-09 ],
          y: m4y, name: measurement_names[4] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[4], },
        },
        { x: [ 0, 0, 0, 0, 1.380695e-07, 1.263535e-07, 1.2029050000000001e-07, 1.1279950000000002e-07, 1.115735e-07, 1.0677950000000001e-07, 1.03112e-07, 1.0186300000000002e-07, 7.394395000000001e-08, 1.3249450000000001e-08, 1.03533e-08, 9.448235000000002e-09, 8.37553e-09, 8.265265e-09, 3.4049500000000003e-09, 3.3365750000000005e-09 ],
          y: m4y, name: measurement_names[5] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[5], },
        },
        { x: [ 0, 0, 0, 0, 3.514505e-09, 2.3927950000000003e-09, 5.123510000000001e-09, 3.44618e-09, 3.4421200000000005e-09, 4.9871800000000006e-09, 3.388335e-09, 3.3802600000000005e-09, 3.6608850000000002e-09, 1.3588600000000002e-08, 1.052855e-08, 9.32114e-09, 1.0444750000000002e-08, 1.0213750000000001e-08, 3.33674e-09, 3.43894e-09 ],
          y: m4y, name: measurement_names[6] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[6], },
        },
        { x: [ 0, 0, 0, 0, 1.1878200000000001e-07, 1.0537750000000002e-07, 1.03534e-07, 9.553030000000001e-08, 9.525755e-08, 9.262450000000001e-08, 9.0012e-08, 9.057455000000001e-08, 6.155670000000001e-08, 1.3401450000000002e-08, 1.0594650000000001e-08, 9.334920000000002e-09, 1.0631750000000002e-08, 1.033955e-08, 3.39179e-09, 3.44994e-09 ],
          y: m4y, name: measurement_names[7] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[7], },
        },
        { x: [ 0, 0, 0, 0, 3.6925650000000003e-09, 2.416005e-09, 5.36898e-09, 3.74072e-09, 3.7242750000000004e-09, 5.1543150000000005e-09, 3.6952400000000006e-09, 3.57121e-09, 3.6658700000000003e-09, 1.276275e-08, 1.0516450000000001e-08, 9.38529e-09, 1.2130450000000001e-08, 1.1887900000000002e-08, 3.3880400000000006e-09, 3.556885e-09 ],
          y: m4y, name: measurement_names[8] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[8], },
        },
        { x: [ 0, 0, 0, 0, 9.767495000000001e-08, 8.445445000000001e-08, 8.678085e-08, 7.77811e-08, 7.750445000000001e-08, 7.90494e-08, 7.798435e-08, 7.646965000000001e-08, 5.058485e-08, 1.2391200000000002e-08, 1.054125e-08, 9.39817e-09, 1.198215e-08, 1.173325e-08, 3.387495e-09, 3.642515e-09 ],
          y: m4y, name: measurement_names[9] + ' (Identity)', type: 'bar', orientation: 'h', yaxis: 'y5', marker: { color: colors[9], },
            textposition: 'outside',
            text: [ "timeout", "timeout", "timeout", "timeout", "71.7ns avg<br>0.0MB", "64.9ns avg<br>0.0MB", "62.7ns avg<br>0.0MB", "57.7ns avg<br>0.0MB", "57.4ns avg<br>0.0MB", "56.1ns avg<br>0.0MB", "53.0ns avg<br>0.0MB", "53.0ns avg<br>0.0MB", "38.2ns avg<br>0.0MB", "13.6ns avg<br>0.0MB", "10.5ns avg<br>0.0MB", "9.39ns avg<br>0.0MB", "8.58ns avg<br>0.0MB", "8.48ns avg<br>0.0MB", "3.30ns avg<br>0.0MB", "<b>3.21ns avg<br>0.0MB</b>" ],
        },
    ];

    var layout = {
        // title: { text: 'RandomFind_200'},
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

    Plotly.newPlot('id_54e74987', data, layout);
</script>