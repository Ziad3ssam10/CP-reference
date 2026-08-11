```cpp
struct XORbasis {

    ll basis[B + 5]{};

    // can make a priority array to the basis

  

    int sz = 0;

    ll base_msk[B + 5]{}, id[B + 5]; // To build base

    void insert(ll x, int idx) {

        ll msk{}; // Remove if there is no build

        for (int i = B; ~i; --i) {

            if (x >> i & 1) {

                if (!basis[i]) {

                    basis[i] = x, ++sz;

                    base_msk[i] = (1LL << i) ^ msk; // Remove if there is no build

                    id[i] = idx; // Remove if there is no build

                    break;

                }

                x ^= basis[i];

                msk ^= base_msk[i]; // Remove if there is no build

            }

        }

    }

  

    bool can(ll x) {

        for (int i = B; ~i; --i) {

            if (x >> i & 1) {

                if (!basis[i])

                    return false;

                x ^= basis[i];

            }

        }

        return true;

    }

    ll max_xor() {

        ll ret = 0;

        for (int i = B; ~i; --i) ret = max(ret, ret ^ basis[i]);

        return ret;

    }

    ll kth_min(ll k) {

        // k is 1 based

        ll tot = 1 << sz, ret{};

        if (k > tot) return -1;

        for (int i = B; ~i; --i) {

            if (!basis[i]) continue;

            tot >>= 1;

            if (tot >= k) {

                ret = min(ret, ret ^ basis[i]);

            } else {

                k -= tot;

                ret = max(ret, ret ^ basis[i]);

            }

        }

        return ret;

    }

    ll get_index(ll x) {

        // Order of x in the distinct elements, *** 1 BASED ***

        if (x < 0) return 0;

        int idx{}, tot = 1 << sz;

        for (int i = B; ~i; --i) {

            if (!basis[i]) continue;

            tot >>= 1;

            if (x >> i & 1) {

                idx += tot;

            }

        }

        return idx + 1;

    }

    ll get_all() {

        // included empty subset

        return (1ll << sz);

    }

    pair<ll, vector<int>> get_max(ll xa) { // Build the indexes of the max subset => Note the build only

        ll msk = 0;

        for (int i = B; ~i; --i) {

            if (!basis[i]) continue;

            ll new_x = xa ^ basis[i];

            if (new_x > xa) {

                msk ^= base_msk[i];

                xa ^= basis[i];

            }

        }

        vector<int> indexes;

        for (int i = B; ~i; --i)

            if (msk >> i & 1) indexes.push_back(id[i]);

        sort(indexes.begin(), indexes.end());

        return {xa, indexes};

    }

};
```