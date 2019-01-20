

# 深度and广度搜索

[TOC]



### 深度优先

用于穷举找到最佳路径

```java
static int max_x = 100;
static int max_y = 100;
static int[][] map = new int[max_x][max_y];// 地图
static int[][] next = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
static boolean[][] mark = new boolean[max_x][max_y];
static int minStep = Integer.MAX_VALUE;


public static void main(String[] args) {
    map[3][2] = 100;    //设置目的
    map[3][1] = 1;      //障碍物
    map[2][2] = 1;
    map[4][2] = 1;
    dfs(0, 0, 0);
    System.out.println(minStep);

}

private static void dfs(int x, int y, int step) {
    if (map[x][y] == 100) {
        minStep = Math.min(step, minStep);
        return;
    }

    for (int i = 0; i < next.length; i++) {
        int next_x = x + next[i][0];
        int next_y = y + next[i][1];
        //判断边界
        //比如20是容忍值
        if (next_x == max_x || next_y == max_y || next_x < 0 || next_y < 0 || mark[next_x][next_y]
                || map[next_x][next_y] == 1 || step >= minStep ||step >20) {
            continue;
        }
        mark[next_x][next_y] = true;
        dfs(next_x, next_y, step + 1);
        mark[next_x][next_y] = false;  //擦出这个点记录
    }
}
```





### 广度优先



找到最先到达目的的方案。

```java
static int max_x = 10;
static int max_y = 10;
static int[][] map = new int[max_x][max_y];// 地图
static int[][] next = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
static boolean[][] mark = new boolean[max_x][max_y];
static int[][] data = new int[100][3];   //存{x,y,step}


static int index_head = 0;
static int index_tail = 1;


public static void main(String[] args) {
    map[3][2] = 100;    //设置目的
    map[3][1] = 1;      //障碍物
    map[2][2] = 1;
    map[4][2] = 1;

    //设置初始data
    data[index_head][0] = 0;
    data[index_head][1] = 0;
    data[index_head][2] = 0;
    bfs();

}

private static void bfs() {
    int source_x;
    int source_y;
    int step = 0;
    boolean flag = true; //是否搜索到
    while (flag) {
        source_x = data[index_head][0];
        source_y = data[index_head][1];
        step = data[index_head][2];

        for (int i = 0; i < next.length; i++) {
            int next_x = source_x + next[i][0];
            int next_y = source_y + next[i][1];
            //判断边界
            if (next_x == max_x || next_y == max_y || next_x < 0 || next_y < 0 || mark[next_x][next_y]
                    || map[next_x][next_y] == 1 ) {
                continue;
            }

            if (map[next_x][next_y] == 100) {
                flag = false;
                step += 1;
                break;
            }
            System.out.println("move to "+next_x+","+next_y);
            mark[next_x][next_y] = true;
            data[index_tail][0] = next_x;
            data[index_tail][1] = next_y;
            data[index_tail][2] = step + 1;
            index_tail ++;
        }

        index_head ++;
    }

    System.out.println(step);
}
```



![1532247851118](C:\Users\ADMINI~1\AppData\Local\Temp\1532247851118.png)



![1532247959606](C:\Users\ADMINI~1\AppData\Local\Temp\1532247959606.png)



