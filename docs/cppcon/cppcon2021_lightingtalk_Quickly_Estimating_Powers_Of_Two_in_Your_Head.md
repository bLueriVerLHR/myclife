# Lightning Talk: Quickly Estimating Powers-Of-Two in Your Head - Andreas Weis - CppCon 2021

The most common 2 powers are as follows:

| power | result |
|-------|--------|
|      1|       2|
|      2|       4|
|      3|       8|
|      4|      16|
|      5|      32|
|      6|      64|
|      7|     128|
|      8|     256|
|      9|     512|
|     10|    1024|

You can also only remember powers like 2, 3, 5, 7 since they are prime numbers.

Find that $2^{10} = 1024 \approx 10^3$. We can easily estimate powers like `a+10b`.

$$
2^{a+10b} =  2^a \times (2^{10})^b \approx 2^a \times 10^{3b},\space 0 < a < 10
$$

| power | $\delta$ |
|-------|----------|
|     10|    0.0234|
|     20|    0.0463|
|     30|    0.0686|
|     40|    0.0905|
|     50|    0.1118|
|    100|    0.2111|
|    150|    0.2994|
|    200|    0.3776|
|    250|    0.4472|
|    300|    0.5090|

> $$
> \delta = 1 - \frac{estimation}{real}
> $$

From the table above, we can see the estimate is good at powers between 10 and 150. But when power gets bigger than 300, the $\delta$ is about 0.5. We can make some changes when reach that large. However, it could be a rare case in practice.

Also, we can estimate 10 power's corresponding 2 power with this method.

$$
10^{a+3b} =  10^a \times 10^{3b} \approx 10^a \times 2^{10b},\space 0 < a < 3
$$