# Strassen算法多线程实现核心代码

### 并行策略

&emsp;&emsp;为所有 $O(n^2)$ 的操作开启一个新线程去计算，每次递归也放入新的线程

### 子任务

#### 计算S，P，C矩阵的子任务类

```java
static class Task2 implements Callable<Matrix> {
    private Matrix A, B;
    private Section sa, sb;
    private int n;
    private Method method;

    public Task2(Matrix A, Matrix B, Section sa, Section sb, int n, Method method) {
        this.A = A;
        this.B = B;
        this.sa = sa;
        this.sb = sb;
        this.n = n;
        this.method = method;
    }

    @Override
    public Matrix call() throws Exception {
        return (Matrix) method.invoke(Matrix.class, A, B, sa, sb, n);
    }
}
```

#### 合并C矩阵的子任务类

```java
static class Task1 implements Callable<Boolean> {
    private Method method;
    private Matrix A, C;

    public Task1(Matrix A, Matrix C, Method method) {
        this.A = A;
        this.C = C;
        this.method = method;
    }

    @Override
    public Boolean call() throws Exception {
        try {
            method.invoke(C, A);
        } catch (IllegalAccessException | InvocationTargetException e) {
            System.out.println(A.row);
            e.printStackTrace();
        }
        return true;
    }
}
```

### 启动新线程处理子任务

```java
private static Matrix RecursiveSolve(Matrix A, Matrix B, Section sectionA, Section sectionB, int n) throws ExecutionException, InterruptedException {
    //n小于等于64时，直接计算比递归更快
    if (n <= 128) {
        return Matrix.multiply(A, B, sectionA, sectionB, n);
    } else {
        Matrix[] S = new Matrix[10];
        Matrix[] P = new Matrix[7];
        //分解为n/2的子矩阵运算，后面运算的阶数都为mid
        int mid = n >> 1;
        //利用一个横纵坐标信息和阶数标记子矩阵
        Section A11 = new Section(sectionA.row, sectionA.column);
        Section A12 = new Section(sectionA.row, sectionA.column + mid);
        Section A21 = new Section(sectionA.row + mid, sectionA.column);
        Section A22 = new Section(sectionA.row + mid, sectionA.column + mid);
        Section B11 = new Section(sectionB.row, sectionB.column);
        Section B12 = new Section(sectionB.row, sectionB.column + mid);
        Section B21 = new Section(sectionB.row + mid, sectionB.column);
        Section B22 = new Section(sectionB.row + mid, sectionB.column + mid);
        Section S11 = new Section(0, 0);
        //启动计算S的任务
        Future<Matrix> future0 = es.submit(new Task2(B, B, B12, B22, mid, minus5));
        Future<Matrix> future1 = es.submit(new Task2(A, A, A11, A12, mid, add5));
        Future<Matrix> future2 = es.submit(new Task2(A, A, A21, A22, mid, add5));
        Future<Matrix> future3 = es.submit(new Task2(B, B, B21, B11, mid, minus5));
        Future<Matrix> future4 = es.submit(new Task2(A, A, A11, A22, mid, add5));
        Future<Matrix> future5 = es.submit(new Task2(B, B, B11, B22, mid, add5));
        Future<Matrix> future6 = es.submit(new Task2(A, A, A12, A22, mid, minus5));
        Future<Matrix> future7 = es.submit(new Task2(B, B, B21, B22, mid, add5));
        Future<Matrix> future8 = es.submit(new Task2(A, A, A11, A21, mid, minus5));
        Future<Matrix> future9 = es.submit(new Task2(B, B, B11, B12, mid, add5));
        //拿到S结果
        S[0] = future0.get();
        S[1] = future1.get();
        S[2] = future2.get();
        S[3] = future3.get();
        S[4] = future4.get();
        S[5] = future5.get();
        S[6] = future6.get();
        S[7] = future7.get();
        S[8] = future8.get();
        S[9] = future9.get();
        //启动计算P的任务
        Future<Matrix> futureP0 = es.submit(new Task2(A, S[0], A11, S11, mid, recur));
        Future<Matrix> futureP1 = es.submit(new Task2(S[1], B, S11, B22, mid, recur));
        Future<Matrix> futureP2 = es.submit(new Task2(S[2], B, S11, B11, mid, recur));
        Future<Matrix> futureP3 = es.submit(new Task2(A, S[3], A22, S11, mid, recur));
        Future<Matrix> futureP4 = es.submit(new Task2(S[4], S[5], S11, S11, mid, recur));
        Future<Matrix> futureP5 = es.submit(new Task2(S[6], S[7], S11, S11, mid, recur));
        Future<Matrix> futureP6 = es.submit(new Task2(S[8], S[9], S11, S11, mid, recur));
        //拿到P的结果
        P[0] = futureP0.get();
        P[1] = futureP1.get();
        P[2] = futureP2.get();
        P[3] = futureP3.get();
        P[4] = futureP4.get();
        P[5] = futureP5.get();
        P[6] = futureP6.get();
        //创建结果矩阵C的四个子矩阵
        Matrix[][] C = new Matrix[2][2];
        C[0][0] = new Matrix(mid);
        C[0][1] = new Matrix(mid);
        C[1][0] = new Matrix(mid);
        C[1][1] = new Matrix(mid);
        //启动计算C的四个子矩阵的任务
        Future<Boolean> C11Complete0 = es.submit(new Task1(P[4], C[0][0], add1));
        Future<Boolean> C11Complete1 = es.submit(new Task1(P[3], C[0][0], add1));
        Future<Boolean> C11Complete2 = es.submit(new Task1(P[1], C[0][0], minus1));
        Future<Boolean> C11Complete3 = es.submit(new Task1(P[5], C[0][0], add1));
        Future<Boolean> C12Complete0 = es.submit(new Task1(P[0], C[0][1], add1));
        Future<Boolean> C12Complete1 = es.submit(new Task1(P[1], C[0][1], add1));
        Future<Boolean> C21Complete0 = es.submit(new Task1(P[2], C[1][0], add1));
        Future<Boolean> C21Complete1 = es.submit(new Task1(P[3], C[1][0], add1));
        Future<Boolean> C22Complete0 = es.submit(new Task1(P[4], C[1][1], add1));
        Future<Boolean> C22Complete1 = es.submit(new Task1(P[0], C[1][1], add1));
        Future<Boolean> C22Complete2 = es.submit(new Task1(P[2], C[1][1], minus1));
        Future<Boolean> C22Complete3 = es.submit(new Task1(P[6], C[1][1], minus1));
        //等待所有任务执行结束
        C11Complete0.get();
        C11Complete1.get();
        C11Complete2.get();
        C11Complete3.get();
        C12Complete0.get();
        C12Complete1.get();
        C21Complete0.get();
        C21Complete1.get();
        C22Complete0.get();
        C22Complete1.get();
        C22Complete2.get();
        C22Complete3.get();

        return merge(C[0][0], C[0][1], C[1][0], C[1][1], mid);
    }
}
```

