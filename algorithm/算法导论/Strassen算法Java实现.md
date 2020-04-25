# Strassen算法Java实现

### 核心递归函数设计

```java
private static Matrix RecursiveSolve(Matrix A, Section sectionA, Matrix B, Section sectionB, int n) {
    //n小于等于64时，直接计算比递归更快
    if (n <= 64) {
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
        //计算10个S矩阵
        S[0] = Matrix.minus(B, B, B12, B22, mid);
        S[1] = Matrix.add(A, A, A11, A12, mid);
        S[2] = Matrix.add(A, A, A21, A22, mid);
        S[3] = Matrix.minus(B, B, B21, B11, mid);
        S[4] = Matrix.add(A, A, A11, A22, mid);
        S[5] = Matrix.add(B, B, B11, B22, mid);
        S[6] = Matrix.minus(A, A, A12, A22, mid);
        S[7] = Matrix.add(B, B, B21, B22, mid);
        S[8] = Matrix.minus(A, A, A11, A21, mid);
        S[9] = Matrix.add(B, B, B11, B12, mid);
        //递归计算7个P矩阵
        P[0] = RecursiveSolve(A, A11, S[0], S11, mid);
        P[1] = RecursiveSolve(S[1], S11, B, B22, mid);
        P[2] = RecursiveSolve(S[2], S11, B, B11, mid);
        P[3] = RecursiveSolve(A, A22, S[3], S11, mid);
        P[4] = RecursiveSolve(S[4], S11, S[5], S11, mid);
        P[5] = RecursiveSolve(S[6], S11, S[7], S11, mid);
        P[6] = RecursiveSolve(S[8], S11, S[9], S11, mid);
        //利用P矩阵死算C结果矩阵
        Matrix[][] C = new Matrix[2][2];
        C[0][0] = new Matrix(mid);
        C[0][0].add(P[4]);
        C[0][0].add(P[3]);
        C[0][0].minus(P[1]);
        C[0][0].add(P[5]);
        C[0][1] = new Matrix(mid);
        C[0][1].add(P[0]);
        C[0][1].add(P[1]);
        C[1][0] = new Matrix(mid);
        C[1][0].add(P[2]);
        C[1][0].add(P[3]);
        C[1][1] = new Matrix(mid);
        C[1][1].add(P[4]);
        C[1][1].add(P[0]);
        C[1][1].minus(P[2]);
        C[1][1].minus(P[6]);
        //返回合并结果
        return merge(C[0][0], C[0][1], C[1][0], C[1][1], mid);
    }
}
```

### 辅助数据结构与函数设计

#### Matrix类

```java
public class Matrix {
    public int[][] M;
    public int row;

    public Matrix(int row) {
        this.row = ex(row);
        //使用Strassen算法的矩阵的n必须是2的整数幂
        M = new int[this.row][this.row];
    }
    ………
}
```

#### 矩阵加减乘方法

##### 用于结果子矩阵合并

```java
public void add(Matrix B) {
    for (int i = 0; i < row; i++) {
        for (int j = 0; j < row; j++) {
            M[i][j] += B.M[i][j];
        }
    }
}

public void minus(Matrix B) {
    for (int i = 0; i < row; i++) {
        for (int j = 0; j < row; j++) {
            M[i][j] -= B.M[i][j];
        }
    }
}
```

#### 用于子矩阵运算

真正参与运算的矩阵为以Section代表的点开始的n阶矩阵

```java
public static Matrix multiply(Matrix A, Matrix B, Section sa, Section sb, int n) {
    Matrix res = new Matrix(n);
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            for (int k = 0; k < n; k++) {
                res.M[i][j] += A.M[sa.row + i][sa.column + k] * B.M[sb.row + k][sb.column + j];
            }
        }
    }
    return res;
}

public static Matrix add(Matrix A, Matrix B, Section sa, Section sb, int n) {
    Matrix res = new Matrix(n);
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            res.M[i][j] = A.M[sa.row + i][sa.column + j] + B.M[sb.row + i][sb.column + j];
        }
    }
    return res;
}

public static Matrix minus(Matrix A, Matrix B, Section sa, Section sb, int n) {
    Matrix res = new Matrix(n);
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            res.M[i][j] = A.M[sa.row + i][sa.column + j] - B.M[sb.row + i][sb.column + j];
        }
    }
    return res;
}
```

### 测试

以下测试仅为初步测试，不作为最终结果使用

**256阶矩阵乘法**

朴素算法：48ms

Strassen算法：110ms

**512阶矩阵乘法**

朴素算法：291ms

Strassen算法：228ms

**1024阶矩阵乘法**

朴素算法：2907ms

Strassen算法：1012ms