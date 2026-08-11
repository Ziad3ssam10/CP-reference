```cpp
for (int mask = 0; mask < (1 << n); ++mask) {

    for (int ii = mask; ; ii = (ii - 1) & mask) {

        // ii is a submask of mask

        if (!ii) break;

    }

}
```