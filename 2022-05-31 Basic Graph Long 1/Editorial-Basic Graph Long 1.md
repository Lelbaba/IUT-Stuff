# [A - Unlock the Lock](https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=3312)
<details>
  <summary>Summary</summary>

  You are given 3 integers $L$, $U$, $R$, and an array $RV$ of $R$ intergers. You can do the following operation in one step-  
  - At each step, choose any interger $x$ from the given array $RV$ and replace $L$ with $(x+L)\mod 10000$.  

  Let us define $distance$ from $i$ to $j$ as the number of operations needed to make $i=j$. Find the minimum distance from $L$ to $U$ 
  or state that it is impossible to make $U$ from $L$ using the above operation.
</details>
<details>
  <summary>Tutorial</summary>

  Let us say, we get $R$ different integers ${L_1}\',{L_2}\',{L_3}\',...,{L_R}\'$ by applying the operation to $L$ once. So we can 
  say that minimum $distance$ from $L$ to all of ${L_i}\'$ is $1$. Because from $L$ there is no other minimal way to visit all ${L_i}\'$. 
  Similarly, from each of the ${L_i}\'$ we will get $x_i$ integers after applying the operation for the second time to all 
  of the ${L_i}\'$. Here $x_i\leq R$, because after applying operation second time, we may get some integers which are the repeatation 
  of ${L_i}\'$.  
  We repeat the steps and at each step, we record the distance of the integers.  

  This above can be implemented using BFS. While iterating a number $x$, we will apply the operation i.even. we will add the $RV_i$ 
  with $x$ and if the number is not visited before, then we will store the number in the queue, and we will update it's distance 
  by $distance[x + RV_i] = distance[x] + 1;$.  
  If it is possible to get $U$ from $L$, then $U$ will be visited once. Otherwise, $U$ will never be visited. (And obviously the 
  queue will not run into $\infty$. Because, we can visit at most 10000 numbers, and after visiting all of these numbers, the new 
  numbers will be already visited.) At the end, if $U$ was visited then the answer is $distance[U]$. And if not, then the answer 
  is $-1$.
</details>
<details>
  <summary>Solution</summary>

  ```cpp
  #include "bits/stdc++.h"

  #define fast ios::sync_with_stdio(0);cin.tie(0)
  #define tests int t=1;if(multi_test)cin>>t;for(int kase=1;kase<=t;kase++)
  #define range(v, n) v, v + n
  #define all(v) v.begin(), v.end()
  #define toN(v, n) v.begin(), v.begin() + n
  #define ulta(v) v.rbegin(), v.rend()

  using namespace std;

  typedef long long ll;
  typedef pair<int, int> PII;

  const bool multi_test = false;
  int l, u, r;
  vector<int> v;

  void solve(int kase) {
    cout << "Case " << kase << ": ";
    vector<int> d(10000, -1);
    d[l] = 0;
    queue<int> q;
    q.push(l);
    while(!q.empty()) {
      int node = q.front();
      q.pop();
      for(auto i: v) {
        int n = (node + i) % 10000;
        if(d[n] == -1) {
          q.push(n);
          d[n] = d[node] + 1;
        }
      }
    }

    cout << (d[u] == -1 ? "Permanently Locked" : to_string(d[u]));
    cout << '\n';
  }

  int main() {
    fast;

    int kase = 1;
    while(cin >> l >> u >> r && r) {
      v.resize(r);
      for(auto &i: v)
        cin >> i;
      solve(kase++);
    }

    return 0;
  }
  ```
</details>

# [B - Knight in a War Grid](https://vjudge.net/problem/UVA-11906)

Let's say we are in cell[ i ][ j ]. The possible moves from this position are:

(i + n, j + m) 

(i + n, j - m)

(i - n, j + m)

(i - n, j - m)

(i + m, j + n)

(i + m, j - n)

(i - m, j + n)

(i - m, j - n)

So, we can iterate through all these cells and count the number of visitable cells i.e. how many of these are inside the field and not filled with water. Then we can check if the count is even or odd. While doing so, if the cell is not already visited, we will go to that cell and do the same. This can be done recursively.

But we haven't considered two cases yet. If n equals m or one of them is 0. In this case, number of visitable cell will just be half of the actual count.

<details>
<summary>C++ Code</summary>

```cpp
#include <bits/stdc++.h>

using namespace std;

int n, m, r, c;
bool can_visit[102][102], visited[102][102];
int cnt[102][102];

bool visitable(int x, int y)
{
    if (x < 0 || x >= r || y < 0 || y >= c)
        return false;
    return can_visit[x][y];
}
void go(int x, int y, int *dx, int *dy)
{
    int visits = 0;
    visited[x][y] = 1;
    for (int i = 0; i < 8; i++)
    {
        int nx = dx[i] + x, ny = dy[i] + y;
        if (visitable(nx, ny))
        {
            visits++;
            if (!visited[nx][ny])
                go(nx, ny, dx, dy);
        }
    }
    if (m == n || !m || !n)
        visits /= 2;
    cnt[x][y] = visits;
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(NULL);

    int T;
    cin >> T;

    for (int t = 0; t < T; t++)
    {
        int w;
        cin >> r >> c >> m >> n >> w;
        for (int i = 0; i < r; i++)
            for (int j = 0; j < c; j++)
                can_visit[i][j] = 1, visited[i][j] = 0;

        memset(cnt, -1, sizeof(cnt));

        int dx[] = {n, n, m, m, -n, -n, -m, -m};
        int dy[] = {m, -m, n, -n, m, -m, n, -n};

        while (w--)
        {
            int x, y;
            cin >> x >> y;
            can_visit[x][y] = 0;
        }

        go(0, 0, dx, dy);

        int evenOddCount[2] = {0};
        for (int i = 0; i < r; i++)
        {
            for (int j = 0; j < c; j++)
            {
                if (cnt[i][j] != -1)
                {
                    evenOddCount[cnt[i][j] % 2]++;
                }
            }
        }
        cout << "Case " << t + 1 << ": " << evenOddCount[0] << " " << evenOddCount[1] << "\n";
    }
    return 0;
}
```
</details>



# [C - Morning Walk](https://vjudge.net/problem/UVA-10596/origin)
We have three things to consider here:

1. We cannot use same edge (road) twice.
2. Every edge must be visited.
3. We have to come back to the initial position

Now considering point 3, if we go to a particular node we also have get out of it. Now if we consider point 1, we need seperate edges to get in and get out of particular node. Thus we can say that every node must have even number of edges connected to it. If some node has odd number of edges connected to it, we cannot visit all the edges fulfilling these conditions.

After we have considered these, we have to check if all of these nodes are connected either directly or indirectly. To check this, we can simply start from any one node that has at least two edges connected to it and perform depth first search or breadh first search to visit the nodes. Once we go to a particular node we mark it as visited. After doing so, we check if all the nodes which have two or more edges connected to them are visited or not. If all are visited, that means they all are connected and we can visit these road fulfilling all the conditions. Otherwise they are not connected and we cannot do so.
<details>
<summary>C++ Code</summary>

```cpp
#include <bits/stdc++.h>

using namespace std;

set<int> adj[202];
bool visited[202];

void dfs(int curr)
{
    visited[curr] = true;

    for(auto child : adj[curr])
    {
        if(visited[child])
            continue;
        dfs(child);
    }
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(NULL);

    int n, r;
    while((cin >> n >> r))
    {
        set<int> to_visit;
        map<int, int> m;
        memset(visited, 0, sizeof(visited));
        for(int i = 0; i < r; i++)
        {
            int u, v;
            cin >> u >> v;
            m[u]++, m[v]++;
            to_visit.insert(u);
            to_visit.insert(v);
            adj[u].insert(v);
            adj[v].insert(u);
        }

        bool f = 1;
        if(to_visit.size() == 0)
            f = 0;

        for(auto it : m)
            if (it.second % 2)
                f = 0;

        if(to_visit.size())
            dfs(*(to_visit.begin()));

        for(auto it : to_visit)
            if(visited[it] == 0)
                f = 0;

        for(int i = 0; i < n; i++)
            adj[i].clear();

        if(f)
            cout << "Possible\n";
        else
            cout << "Not Possible\n";
    }
    return 0;
}
```
</details>


# [D - Crazy King](https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2327)
<details>
  <summary>Summary</summary>

  You are given a $N\times M$ chessboard which contains any of these symbols $\\{'.','Z','A','B'\\}$. Cells containing $'Z'$ are the cells occupied by knights. $'.'$ means unoccupied cells.  

  You have to go to $'B'$ from $'A'$ satisfying the conditions below:  
  - You can move forward or backward by one step at a time diagonally, horizontally or 
  vertically i.e. like a king in a chessboard. (Because you are the king!)
  - You cannot move to a cell which is occupied by a knight.
  - If any of the knights can move to a cell in one move, then you cannot move to that 
  cell i.e. all accessible cells from the knights are blocked for you.  

  Find minimum distance from $B$ to $A$, or if it is not possible then, print as requested in the statement.
</details>
<details>
  <summary>Tutorial</summary>

  We can traverse through all possible accessible cells using BFS. While we are on a cell, 
  if the child cells (the cells to which we can go in one move) are not blocked by the above conditions, then we can visit the child cells and update its distance by 
  $distance[child]=distance[parent]+1$.  

  *How can we check if a cell is blocked?*  
  If we are on a cell and in one knight move from that cell we can go to a cell which is occupied by a knight then obviously our present cell is blocked. Another way to do this 
  is, before applying bfs, we can visit all the cells which are occupied by knights and 
  from each cell we can make one knight move and mark the new cells as blocked.  

  After completing this journey print the $distance[B]$ or print as requested in the statement if $B$ was never visited.
</details>
<details>
  <summary>Solution</summary>

  ```cpp
  #include "bits/stdc++.h"

  #define fast ios::sync_with_stdio(0);cin.tie(0)
  #define tests int t=1;if(multi_test)cin>>t;for(int kase=1;kase<=t;kase++)
  #define caseout cout << "Case " << kase << ": "
  #define range(v, n) v, v + n
  #define all(v) v.begin(), v.end()
  #define toN(v, n) v.begin(), v.begin() + n
  #define ulta(v) v.rbegin(), v.rend()

  using namespace std;

  typedef long long ll;
  typedef pair<int, int> PII;

  const bool multi_test = true;
  int m, n;
  PII a, b;
  int d[101][101];
  vector<string> grid;
  vector<PII> horse_dir{{2, 1}, {-2, 1}, {2, -1}, {-2, -1},
                  {1, 2}, {-1, 2}, {1, -2}, {-1, -2}};
  vector<PII> king_dir{{1, 1}, {-1, 1}, {1, -1}, {-1, -1},
                      {1, 0}, {-1, 0}, {0,  1}, { 0, -1}};

  void findab() {
    for(int i = 0; i < m; i++) {
      for(int j = 0; j < n; j++) {
        if(grid[i][j] == 'A')
          a = {i, j};
        if(grid[i][j] == 'B')
          b = {i, j};
      }
    }
  }

  bool is_valid(int x, int y) {
    if(x < 0 || y < 0 || x >= m || y >= n || grid[x][y] == 'Z')
      return false;
    if(grid[x][y] == 'B')
      return true;
    for(auto [s, t]: horse_dir) {
      int X = x + s, Y = y + t;
      if(X >= 0 && Y >= 0 && X < m && Y < n && grid[X][Y] == 'Z')
        return false;
    }
    return true;
  }

  void solve() {
    cin >> m >> n;
    grid.resize(m);
    for(auto &i: grid)
      cin >> i;

    findab();
    memset(d, -1, sizeof d);
    queue<PII> q;
    q.push(a);
    d[a.first][a.second] = 0;
    while(!q.empty()) {
      int x = q.front().first, y = q.front().second;
      q.pop();
      for(auto [s, t]: king_dir) {
        int X = x + s, Y = y + t;
        if(is_valid(X, Y) && d[X][Y] == -1) {
          q.push({X, Y});
          d[X][Y] = d[x][y] + 1;
        }
      }
    }

    if(d[b.first][b.second] == -1)
      cout << "King Peter, you can't go now!";
    else
      cout << "Minimal possible length of a trip is " << d[b.first][b.second];
    cout << '\n';
  }

  int main() {
    fast;

    tests
      solve();

    return 0;
  }
  ```
</details>

# [E - Sticker Collector Robot](https://vjudge.net/problem/UVA-11831)
If you look at how the directions change, the robot ends up facing North(0 deg), South(180 deg), East(90 deg) or West(270 deg) because we add 90 deg to the current direction when the instruction is 'D' and subtract 90 deg when the instruction os 'E'. We use this idea of four distinct angles to set the starting direction:

<details>
  <summary>Code to set directions</summary>
    
 ```cpp 
if(g[i][j]  == 'N')
{
    deg = 0;
    kx = i;
    ky = j;
}
if(g[i][j]  == 'S')
{
    deg = 180;
    kx = i;
    ky = j;
}
if(g[i][j]  == 'L')
{
    deg = 90;
    kx = i;
    ky = j;
}
if(g[i][j]  == 'O')
{
    deg = 270;
    kx = i;
    ky = j;
}
```
</details>
  
<br>
Using only four angles to represent the direction simplifies things i.e.less angle ranges to look out for :) . Hence, every time we subtract or add 90 we do the following operation:

```cpp
deg %= 360;
```

<br>  
Also note that any time the angle is negative i.e. an 'E' instruction(subtract 90 deg) when the robot is facing North(0 deg), we can add 360 to it get a positive value which would equal the same direction change - we'll end up at the same positon. Look at the graph for further clarification:

<details>
  <summary>Graph</summary>
  
![image](https://user-images.githubusercontent.com/52543544/172056758-d78c7a84-6552-4411-8b1d-295b06a8abcb.png)
</details>
   
<br>   
Thus, for every 'D' or 'E' instruction we adjust the robot's position using this:
<details>
  <summary>Code to change directions</summary>
    
```cpp
void change_dir(char c)
{
    if(c == 'D')
    {
        deg += 90;
    }
    else if(c == 'E')
    {
        deg -= 90;
        if(deg<0){
            deg+=360;
        }
    }
    deg %= 360;
}
```
</details>

<br>
For every 'F' instruction we move:<br>
- one row up when the current orientation is 0 deg,  <br>
- one column right when the orientation is 90 deg,<br>
- one row down when the current orientation is 180 deg,  <br>
- one column left when the orientation is is 270 deg.<br>
<br>
<br>

We should check that the 'F' instructions don't make the robot move out of the grid thus we add conditions that the robot only moves if the positions are limited to (0<= current_row <= r) && (0<= current_column <= c). We should also make sure that the robot doesn't move if the 'F' instruction tells it to move into a cell marked with '#' . If the robot moves into a cell that has a sticker we add it to the number collected and mark the position with a '.' so that if the cell is revisited, it doesn't count the sticker again.

<details>
<summary>C++ Code</summary>

```cpp
#include <bits/stdc++.h>

using namespace std;
#define ll long long
int t, n, m;
int deg = 0;
vector<vector<char>> g(110, vector<char>(110));

void change_dir(char c)
{
    if(c == 'D')
    {
        deg += 90;
    }
    else if(c == 'E')
    {
        deg -= 90;
        if(deg<0){
            deg+=360;
        }
    }
    deg %= 360;
}

bool isValid(int x, int y)
{
    if(x < 0 || y < 0 || x >= n || y >= m)
    {
        return false;
    }

    if(g[x][y] == '#')
    {
        return false;
    }

    return true;
}

void make_move(int &x, int &y)
{
    if(deg == 0)
    {
        if(isValid(x - 1, y))
        {
            --x;
        }

    }
    if(deg == 90)
    {
        if(isValid(x, y + 1))
        {
            ++y;
        }

    }
    if(deg == 180)
    {
        if(isValid(x + 1, y))
        {
            ++x;
        }

    }
    if(deg == 270)
    {
        if(isValid(x, y - 1))
        {
            --y;
        }

    }

}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    
    int kx, ky, px, py,s;
    while(cin>>n>>m>>s)
    {

        if(n==m && m==s && m==0){
            break;
        }
        int stickers =0;
        

        int buff;
        bool lost = 0;
        for (int i = 0; i < n ; ++i)
        {
            for (int j = 0; j < m ; ++j)
            {
                cin >> g[i][j];
                if(g[i][j]  == 'N')
                {
                    deg = 0;
                    kx = i;
                    ky = j;
                }
                if(g[i][j]  == 'S')
                {
                    deg = 180;
                    kx = i;
                    ky = j;
                }
                if(g[i][j]  == 'L')
                {
                    deg = 90;
                    kx = i;
                    ky = j;
                }
                if(g[i][j]  == 'O')
                {
                    deg = 270;
                    kx = i;
                    ky = j;
                }

            }

        }

        string c;

        cin>>c;
        for(auto i:c){
            if(i=='D' || i=='E'){
                change_dir(i);
            }

            else if(i=='F'){
                make_move(kx, ky);

                if(g[kx][ky] == '*'){

                    // cout << deg << " " << kx << " " << ky << "\n";
                    g[kx][ky] = '.';
                    stickers++;
                }   
            }
        }

        cout << stickers ;

        cout << "\n";


    }
}
```
</details>

# [F - Forwarding Emails](https://vjudge.net/problem/UVA-12442/origin)
This can be solved using dfs. We keep traversing until we find a node that is already visited. If we find a node that is already visited, it means we have found a cycle. So, we stop there and return the length. Note that we have to store the length of the chain in another array. Otherwise we will get TLE.

<details>
<summary>Code</summary>

```cpp
#include <bits/stdc++.h>

using namespace std;

int T, N, a, b;
vector<int> graph, sum;
vector<bool> vis;

int dfs(int u)
{
    vis[u] = true;
    int tot = 0;
    if (graph[u] != -1 && !vis[graph[u]])
        tot += 1 + dfs(graph[u]);
    vis[u] = false;
    return sum[u] = tot;
}

int main()
{
    cin >> T;
    for (int t = 1; t <= T; t++)
    {
        cin >> N;
        graph.assign(N, -1);
        sum.assign(N, -1);
        vis.assign(N, false);
        for (int i = 0; i < N; i++)
        {
            cin >> a >> b;
            graph[a - 1] = b - 1;
        }
        int ans = 0, best_len = 0;
        for (int i = 0; i < N; i++)
        {
            if (sum[i] == -1)
                dfs(i);
            if (sum[i] > best_len)
            {
                best_len = sum[i];
                ans = i;
            }
        }
        cout << "Case " << t << ": " << ans+1 << "\n";
    }
}
```
</details>

# [G - Strongly Connected City](https://vjudge.net/problem/CodeForces-475B)
For this problem, almsot any approach will work. We start from a cell and try to travel to all the cells we can go from current cell using dfs, bfs or flood-fill and mark them visited. If all the cells are visited we reset the visited array and continue this process for all other cells as the starting cell. If for some cell we cannot visit all the cells, the ans is NO. Otherwise it is YES.
<details>
<summary>Code</summary>

```cpp
#include <bits/stdc++.h>

using namespace std;

int colDirection[22];
int rowDirection[22];
bool visited[22][22];
int n, m;

void reset()
{
    for (int i = 0; i < 22; i++)
        for (int j = 0; j < 22; j++)
            visited[i][j] = 0;
}

int flood_fill(int x, int y)
{
    if (x >= n || x < 0 || y >= m || y < 0 || visited[x][y])
    {
        return 0;
    }

    int ret = 1;
    visited[x][y] = true;
    ret += flood_fill(x, y + colDirection[x]);
    ret += flood_fill(x + rowDirection[y], y);
    return ret;
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(NULL);

    cin >> n >> m;
    for (int i = 0; i < n; i++)
    {
        char ch;
        cin >> ch;
        if (ch == '<')
            colDirection[i] = -1;
        else
            colDirection[i] = 1;
    }
    for (int i = 0; i < m; i++)
    {
        char ch;
        cin >> ch;
        if (ch == '^')
            rowDirection[i] = -1;
        else
            rowDirection[i] = 1;
    }
    bool f = 1;
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < m; j++)
        {
            int ret = flood_fill(i, j);
            f &= (ret == n * m);
            reset();
        }
    }
    if (f)
        cout << "YES\n";
    else
        cout << "NO\n";
    return 0;
}
```
</details>

# [H - Tree Recovery](https://vjudge.net/problem/UVA-536)

We simply traverse the left subtree then the right subtree and output the root node elements. Code is commented for further clarification. <br>
<br>
Idea and code(kind-of :3) from [here](https://www.cnblogs.com/mofushaohua/p/7789353.html)
<details>
<summary>C++ Code</summary>

```cpp
#include <bits/stdc++.h>

using namespace std;
#define ll long long


string pre, in, post;



void construct(int l1, int r1, int l2, int r2)
{

    if(l1 > r1)
    {
        return ; //no more elements left to check
    }


    int root_in_inorder = l2; //we consider the root in inorder is at the index passed as l2

    for(;; root_in_inorder++)
    {
        // we then see if index we considered is actually the position of the root else we update
        if(pre[l1] == in[root_in_inorder])
        {
            break;
        }
    }


    //number of elements to the left of the root in the the inorder
    int number_of_elements_in_left_subtree = root_in_inorder - l2;

    //recurse the left subtree
    construct(l1 + 1, l1 + number_of_elements_in_left_subtree, l2, root_in_inorder - 1);
    //recurse the right subtree
    construct(l1 + 1 + number_of_elements_in_left_subtree, r1, root_in_inorder + 1, r2);
    //print root
    cout << in[root_in_inorder];
}
int main()
{



    while(cin >> pre >> in)
    {

        construct(0, pre.size() - 1, 0, in.size() - 1);;

        cout << "\n";


    }
}
```
</details>

# [I - Bicoloring](https://vjudge.net/problem/UVA-10004)

Okay so can we say a graph(fully connected) is bicolorable when the adjacent nodes are not the same color as the present color which a node has. This problem can be done by many approaches. We will see a recursive version using DFS. 
We may declare function DFS(int i, int color). i will be the node and the 'color' parameter will be the color by which we will paint the node. We will keep a vis[] array to keep record of the nodes we have visited by far.

<b>Approach :-</b> We wil enter a node with the color by which we want to paint that node. Since there can be only 2 colors, we can say one color can be 0 and another be 1. If the color is 0, we will go to the next state with color = 1 and vice versa. We will visit the nodes as we enter them.

<b>Base Case :-</b> Whenever any node is visited and color of the next node is the same as the color of the previous node, we will return Good = false (a global variable which will already be true).

<b>Main :- </b>We will create the graph into an adjacency list. Then after doing DFS from the 0th index, we will just check the value of Good. If it is true, then it is Bicolorable, otherwise, it's not.

<b>Complexity :-</b> O(Nodes + Edges)
<details>
<summary>Code</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define flag bool
#define el "\n"
#define nl cout << "\n";
#define ff first
#define ss second
#define stp setprecision
#define fixed_stp fixed << setprecision
#define yeah cout << "YES" << el
#define nope cout << "NO" << el
#define map_print(mp) for (auto itr : mp) cout << itr.ff << " " << itr.ss << el;
#define vec_print(v) for  (auto itr : v) cout << itr << " "; nl;
#define freopen()   freopen("inputd.txt", "r", stdin); \
                    freopen("outputd.txt", "w", stdout);
#define fio ios_base::sync_with_stdio(0); cin.tie(NULL); cout.tie(NULL);
#define pi 2 * acos(0.0)
ll const Mod = 1e18;
const int N = 1e9 + 7; 
// ===== // 
int l, n; 
vector <bool> vis(2010);
vector <vector <int> > v(2010);
vector <int> col(2010);
bool Good = true;
void DFS(int i, int color)
{
    vis[i] = true;
    col[i] = color;
    for(auto i : v[i])
    {
        if(vis[i])
        {
            if(col[i] == color) 
            {
                Good = false;
                return;
            }
            else continue;
        }
        
        if(color == 0)
        DFS(i, 1);
        else
        DFS(i, 0);
    }
}
int main()
{
    fio;
    while(cin >> n)
    {
        if(!n) break;        
        Good = true;
        vis.clear(), v.clear(), col.clear();
        vis.resize(n); v.resize(n); col.resize(n);
        cin >> l;
        while(l--)
        {
            int a, b; cin >> a >> b;
            v[a].push_back(b);
            v[b].push_back(a);
        }
        l = v.size();
        DFS(0,0);
        if(Good) cout << "BICOLORABLE." << el;
        else cout << "NOT BICOLORABLE." << el;
    }
    
    return 0;
}
```
</details>

# [J - Party](https://vjudge.net/problem/CodeForces-177C2/origin)
First of all, we have to make sets of friends who know each other directly or indirectly. To do this we can use the concept of Disjoint Set Union.<br>
At first we take an array P, where P[<i>i</i>] donotes the parent of element <i>i</i>. To join two elements <i>u</i> and <i>v</i>, we at first find the parent of <i>u</i> and <i>v</i>, P[<i>u</i>] and P[<i>v</i>] respectively. Now we take another array which stores the size of set that the parent is a part of. Let's say P[<i>u</i>] is a part of greater set. So, we set the parent of P[<i>v</i>] to P[<i>u</i>] i.e P[P[<i>v</i>]] = P[<i>u</i>] and update the size of P[<i>u</i>].<br>
<details>
<summary>How to find parent or the root</summary>
<p>Initially, we set the parent of every element to themselves, namely, P[i] = i. So, whenever we find such a node for which P[<i>node</i>] holds the value <i>node</i>, we can be sure that it is the root node. Otherwise we find the parent of P[<i>node</i>] and so on.</p>

```cpp
int find_parent(int node)
{
    if(p[node] == node)
        return node;

    return p[node] = find_parent(p[node]);
}
```
</details>
Whenever two friends know each other, we can join them by the above process. After we have joined all of them, we have to see if two friends, who hate each other, belong to same set or not. To check this, we simply find the root node of both of them. If they have same root node, they belong to the same set. If they belong to the same set, we cannot invite that set of friends even if they are the largest group. So, we exclude that set from consideration. After excluding the unwanted sets. We iterate through the size of the remaining sets and find the largest size. This is the answer.
<br><br>
<details>
<summary>C++ Code</summary>

```cpp
#include <bits/stdc++.h>

using namespace std;

#define MAXN 2003

int p[MAXN], sz[MAXN];

int find_parent(int node)
{
    if(p[node] == node)
        return node;
    return p[node] = find_parent(p[node]);
}

void join(int u, int v)
{
    int pu = find_parent(u);
    int pv = find_parent(v);
    
    if (pu == pv)
        return;
    else if (sz[pu] > sz[pv])
    {
        p[pv] = pu;
        sz[pu] += sz[pv];
    }
    else
    {
        p[pu] = pv;
        sz[pv] += sz[pu];
    }
    return;
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(NULL);

    int n, k, m;
    set<int> root;

    for (int i = 0; i < MAXN; i++)
    {
        p[i] = i;
        sz[i] = 1;
    }

    cin >> n >> k;

    for (int i = 0; i < k; i++)
    {
        int u, v;
        cin >> u >> v;
        join(u, v);
    }

    for (int i = 1; i < n + 1; i++)
    {
        root.insert(find_parent(i));
    }
    
    cin >> m;
    for (int i = 0; i < m; i++)
    {
        int u, v;
        cin >> u >> v;
        if(find_parent(u) == find_parent(v))
        {
            root.erase(find_parent(u));
        }
    }

    int mx = 0;
    for (auto node : root)
        mx = max(mx, sz[node]);
    cout << mx;

    return 0;
}
```
</details>

# [K - Permutation Cycles](https://vjudge.net/problem/CodeChef-PCYCLE)

The solution is pretty straightforward. We literally have to follow the steps told in the problem statement and there’s our answer. We will find how many cycles exist in the array. We will go to index i and then go to a[i] considering itself as the next index. This problem can be easily solved by a recursive approach. We will just go to an index and consider that index a part of our cycle. We take an array which will keep a record of which index/elements we have visited by far. 
We can create a function - let’s say DFS(int i, vector <int> &a). 


<b>Base case :-</b> Whenever we find the index by which we have started the recursion, we will return all of the index/elements we have visited by far in that cycle. This can simply be detected by checking whether vis[i] is true or false. We will return when vis[i] is true and add the starting i to that cycle to complete the cycle. 

<b>Approach :-</b> Here i is the index from where we will start the cycle and end as well. Whenever any index/element is not visited, we will push i to the vector a and visit that i.e vis[i] = true. When we reach our base case, we will push the whole vector a into a vector of vectors let’s call ans. Here our cycles will be stored.

<b>Main :-</b> We will iterate through the whole array of inputs and once we get our ans in the vector of vectors ans, we will just print the size of the vector as it represents how many cycles existed in the input array.

<b>Coplexity :- </b>O(N)

<details>
<summary>Code</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define flag bool
#define el "\n"
#define nl cout << "\n";
#define ff first
#define ss second
#define stp setprecision
#define fixed_stp fixed << setprecision
#define yeah cout << "YES" << el
#define nope cout << "NO" << el
#define map_print(mp) for (auto itr : mp) cout << itr.ff << " " << itr.ss << el;
#define vec_print(v) for  (auto itr : v) cout << itr << " "; nl;
#define freopen()   freopen("inputd.txt", "r", stdin); \
                    freopen("outputd.txt", "w", stdout);
#define fio ios_base::sync_with_stdio(0); cin.tie(NULL); cout.tie(NULL);
#define pi 2 * acos(0.0)
ll const Mod = 1e18;
const int N = 1e9 + 7; 
// ===== // 
int l, n; 
vector <bool> vis(2010);
vector <vector <int> > v(2010);
vector <int> col(2010);
bool Good = true;
void DFS(int i, int color)
{
    vis[i] = true;
    col[i] = color;
    for(auto i : v[i])
    {
        if(vis[i])
        {
            if(col[i] == color) 
            {
                Good = false;
                return;
            }
            else continue;
        }
        
        if(color == 0)
        DFS(i, 1);
        else
        DFS(i, 0);
    }
}
int main()
{
    fio;
    while(cin >> n)
    {
        if(!n) break;        
        Good = true;
        vis.clear(), v.clear(), col.clear();
        vis.resize(n); v.resize(n); col.resize(n);
        cin >> l;
        while(l--)
        {
            int a, b; cin >> a >> b;
            v[a].push_back(b);
            v[b].push_back(a);
        }
        l = v.size();
        DFS(0,0);
        if(Good) cout << "BICOLORABLE." << el;
        else cout << "NOT BICOLORABLE." << el;
    }
    
    return 0;
}
```
</details>
