```cpp
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
typedef pair<int,int> ii;
typedef vector<int> vi;
typedef vector<ii> vii;
typedef long double ld;

#define fi first
#define se second
#define pb push_back
#define mp make_pair

ll dp[101][101][1001][3]; 
/*
dp[i][j][k][l] : 
i - number of numbers placed
j - number of connected components
k - total sum currently (filling empty spaces with a_{i} (0-indexed)
l - number of endpoints that are filled
*/
ll a[101];
const ll MOD = 1e9 + 7;

int main()
{
	ios_base::sync_with_stdio(0); cin.tie(0);
	int n, l;
	cin>>n>>l;
	for(int i = 0; i < n; i++)
	{
		cin>>a[i];
	}
	sort(a, a + n);
	if(n == 1) //special case
	{
		cout << 1;
		return 0;
	}
	a[n] = 10000; //inf for simplicity
	if(a[1] - a[0] <= l) dp[1][1][a[1] - a[0]][1] = 2; //fill a[0] at one of the endpoints, there are 2 endpoints to fill.
	if(2*(a[1] - a[0]) <= l) dp[1][1][2*(a[1] - a[0])][0] = 1; //fill a[0] in the middle, positions doesn't matter.
	
	for(int i = 1; i < n; i++)
	{
		int diff = a[i + 1] - a[i]; //this thing is "INF" if i = n - 1.
		for(int j = 1; j <= i; j++)
		{
			for(int k = 0; k <= l; k++)
			{
				for(int z = 0; z < 3; z++)
				{
					if(!dp[i][j][k][z]) continue; //this value does not exist
					//First, we try to fill one of the ends
					if(z < 2 && k + diff*(2*j - z - 1) <= l) //there are 2*j - z - 1 positions that we're supposed to "upgrade" (-1 because one of the positions is merged with the endpoints after this move)
					{
						if(i == n - 1)
						{
							dp[i + 1][j][k + diff*(2*j - z - 1)][z + 1] = (dp[i + 1][j][k + diff*(2*j - z - 1)][z + 1] + dp[i][j][k][z]*(2-z)*j)%MOD; //we have j con. comp. to choose to merge with
						}
						else if(z == 0 || j > 1) //otherwise this coincides with i == n - 1
						{
							dp[i + 1][j][k + diff*(2*j - z - 1)][z + 1] = (dp[i + 1][j][k + diff*(2*j - z - 1)][z + 1] + dp[i][j][k][z]*(2-z)*(j-z))%MOD; //can only merge with the con comp. that are not connected to ends.
						}
						if(k + diff*(2*j - z + 1) <= l) //now we create a new cc.
						{
							dp[i + 1][j + 1][k + diff*(2*j - z + 1)][z + 1] = (dp[i + 1][j + 1][k + diff*(2*j - z + 1)][z + 1] + dp[i][j][k][z]*(2-z))%MOD; //we can choose one of the ends to create
						}
					}
					//Next, we dont fill the ends. 
					//Part 1 : Create new cc
					if(k + diff*(2*j - z + 2) <= l) //2 new positions to "upgrade"
					{
						dp[i + 1][j + 1][k + diff*(2*j - z + 2)][z] = (dp[i + 1][j + 1][k + diff*(2*j - z + 2)][z] + dp[i][j][k][z])%MOD; //nothing new happens
					}
					//Part 2 : Stick to one cc
					if(k + diff*(2*j - z) <= l) //no new positions to "upgrade"
					{
						dp[i + 1][j][k + diff*(2*j - z)][z] = (dp[i + 1][j][k + diff*(2*j - z)][z] + dp[i][j][k][z]*(2*j - z))%MOD; //we can merge in 2*j - z possible positions
					}
					//Part 3 : Merge two ccs together
					if((k + diff*(2*j - z - 2) <= l) && (j >= 2) && (i == n - 1 || j > 2 || z < 2))
					{
						if(z == 0)
						{
							dp[i + 1][j - 1][k + diff*(2*j - z - 2)][z] = (dp[i + 1][j - 1][k + diff*(2*j - z - 2)][z] + dp[i][j][k][z]*j*(j-1))%MOD; //there are jP2 possible merges
						}
						if(z == 1)
						{
							dp[i + 1][j - 1][k + diff*(2*j - z - 2)][z] = (dp[i + 1][j - 1][k + diff*(2*j - z - 2)][z] + dp[i][j][k][z]*(j-1)*(j-1))%MOD; //there are (j-1)P2+(j-1) merges
						}
						if(z == 2)
						{
							if(i == n - 1)
							{
								dp[i + 1][j - 1][k + diff*(2*j - z - 2)][z] = (dp[i + 1][j - 1][k + diff*(2*j - z - 2)][z] + dp[i][j][k][z])%MOD; //there's only 1 place it can go.
							}
							else
							{
								dp[i + 1][j - 1][k + diff*(2*j - z - 2)][z] = (dp[i + 1][j - 1][k + diff*(2*j - z - 2)][z] + dp[i][j][k][z]*(j-2)*(j-1))%MOD; //there're (j-2)P2 + 2(j-2) possiblilities
							}
						}
					}
				}
			}
		}
	}
	
	ll answer = 0;
	for(int i = 0; i <= l; i++)
	{
		answer = (answer + dp[n][1][i][2])%MOD; //sum the dp values for all possible sums
	}
	cout << answer << '\n';
	return 0;
}

```