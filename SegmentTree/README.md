### Segment Tree（セグメントツリー）とは
**Segment Tree** は、配列の特定の範囲に対する集約値（最大値、最小値、合計値、GCD など）を効率的に求めたり更新したりするためのデータ構造です。

#### 主な用途
- **範囲クエリ**: 配列の指定された範囲に対する値（例: 最大値、最小値、合計値）を高速に計算。
- **ポイント更新**: 配列内の値を変更し、その変更を即座に反映。

#### 特徴
- **クエリ処理時間**: \(O(\log N)\)
- **更新処理時間**: \(O(\log N)\)
- **構築時間**: \(O(N)\)

#### セグメントツリーの構造
セグメントツリーは配列を二分木に拡張したものです。  
- 葉ノード（最下層）: 元の配列の各要素を格納。
- 内部ノード: そのノードがカバーする区間の集約値を格納。

例えば、長さ \(N=8\) の配列を最大値で構築する場合、次のようなツリーが構築されます：
```
元の配列:    [5, 2, 4, 7, 1, 3, 6, 8]
セグメントツリー:
                     8
           /                   \
         7                       8
      /     \                 /     \
    5         7           3         8
  /   \     /   \       /   \     /   \
 5     2   4     7     1     3   6     8
```

---

### Segment Tree のアルゴリズム

#### 1. **構築 (Build)**
セグメントツリーを構築する際、元の配列のデータを利用して木を作ります。

##### アルゴリズム
1. 葉ノードに元の配列の値を割り当てる。
2. 内部ノードは、その子ノードの集約値を計算して格納。

##### 時間計算量
- \(O(N)\): \(N\) 個の葉ノードを構築し、それを基に内部ノードを計算。

---

#### 2. **クエリ (Query)**
指定された範囲の値（最大値、最小値、合計値など）を取得します。

##### アルゴリズム
1. 範囲 \([L, R]\) が現在のノードの区間に完全に含まれている場合、そのノードの値を返す。
2. 範囲 \([L, R]\) が現在のノードの区間と交差する場合、子ノードを再帰的に探索し、その結果を統合する。
3. 範囲 \([L, R]\) が現在のノードの区間と重ならない場合、無視する。

##### 時間計算量
- \(O(\log N)\): 各レベルで分割統治を行うため、探索の深さは木の高さに比例。

---

#### 3. **更新 (Update)**
配列内の値を変更した際、その変更をセグメントツリーに反映します。

##### アルゴリズム
1. 葉ノードの値を更新。
2. 更新された葉ノードに影響を受ける親ノードを再帰的に更新。

##### 時間計算量
- \(O(\log N)\): 木の高さ分だけ再帰的に更新を行う。

---
以下は、**JavaScript** と **TypeScript** でセグメントツリーのアルゴリズムを実装する方法について説明します。

---

### **セグメントツリーの基本構造**
セグメントツリーは、配列の範囲クエリを高速に処理するための木構造です。  
配列の要素を「葉ノード」とし、範囲の集約値（最大値、最小値、合計など）を「内部ノード」に格納します。

---

### **JavaScript 実装**

#### **基本クラス構造**
```javascript
class SegmentTree {
  constructor(arr) {
    this.n = arr.length;
    this.tree = new Array(4 * this.n).fill(0); // セグメントツリー用配列
    this.build(arr, 0, 0, this.n - 1); // ツリーの構築
  }

  // ツリーを構築する
  build(arr, node, start, end) {
    if (start === end) {
      // 葉ノード
      this.tree[node] = arr[start];
    } else {
      const mid = Math.floor((start + end) / 2);
      const leftNode = 2 * node + 1;
      const rightNode = 2 * node + 2;

      this.build(arr, leftNode, start, mid); // 左の子ノード
      this.build(arr, rightNode, mid + 1, end); // 右の子ノード

      // 内部ノードの値を計算（最大値の場合）
      this.tree[node] = Math.max(this.tree[leftNode], this.tree[rightNode]);
    }
  }

  // 範囲クエリ
  query(node, start, end, l, r) {
    if (r < start || l > end) {
      // クエリ範囲外
      return -Infinity;
    }
    if (l <= start && end <= r) {
      // 完全にクエリ範囲内
      return this.tree[node];
    }
    // 部分的にクエリ範囲に重なる場合
    const mid = Math.floor((start + end) / 2);
    const leftQuery = this.query(2 * node + 1, start, mid, l, r);
    const rightQuery = this.query(2 * node + 2, mid + 1, end, l, r);
    return Math.max(leftQuery, rightQuery);
  }

  // 範囲クエリを簡単に呼び出す
  rangeQuery(l, r) {
    return this.query(0, 0, this.n - 1, l, r);
  }

  // 値の更新
  update(node, start, end, idx, value) {
    if (start === end) {
      // 葉ノードの更新
      this.tree[node] = value;
    } else {
      const mid = Math.floor((start + end) / 2);
      const leftNode = 2 * node + 1;
      const rightNode = 2 * node + 2;

      if (idx <= mid) {
        // 左の子ノードを更新
        this.update(leftNode, start, mid, idx, value);
      } else {
        // 右の子ノードを更新
        this.update(rightNode, mid + 1, end, idx, value);
      }

      // 内部ノードの更新
      this.tree[node] = Math.max(this.tree[leftNode], this.tree[rightNode]);
    }
  }

  // 値の更新を簡単に呼び出す
  pointUpdate(idx, value) {
    this.update(0, 0, this.n - 1, idx, value);
  }
}
```

#### **使用例**
```javascript
const arr = [1, 3, 5, 7, 9, 11];
const segTree = new SegmentTree(arr);

// 範囲 [1, 4] の最大値を取得
console.log(segTree.rangeQuery(1, 4)); // 出力: 9

// 配列の 3 番目の値を 6 に更新
segTree.pointUpdate(3, 6);

// 更新後の範囲 [1, 4] の最大値を取得
console.log(segTree.rangeQuery(1, 4)); // 出力: 9
```

---

### **TypeScript 実装**

#### **基本クラス構造**
TypeScript の型安全性を活かした実装です。

```typescript
class SegmentTree {
  private tree: number[];
  private n: number;

  constructor(arr: number[]) {
    this.n = arr.length;
    this.tree = new Array(4 * this.n).fill(0); // セグメントツリー用配列
    this.build(arr, 0, 0, this.n - 1); // ツリーの構築
  }

  // ツリーを構築する
  private build(arr: number[], node: number, start: number, end: number): void {
    if (start === end) {
      // 葉ノード
      this.tree[node] = arr[start];
    } else {
      const mid = Math.floor((start + end) / 2);
      const leftNode = 2 * node + 1;
      const rightNode = 2 * node + 2;

      this.build(arr, leftNode, start, mid); // 左の子ノード
      this.build(arr, rightNode, mid + 1, end); // 右の子ノード

      // 内部ノードの値を計算（最大値の場合）
      this.tree[node] = Math.max(this.tree[leftNode], this.tree[rightNode]);
    }
  }

  // 範囲クエリ
  private query(node: number, start: number, end: number, l: number, r: number): number {
    if (r < start || l > end) {
      // クエリ範囲外
      return -Infinity;
    }
    if (l <= start && end <= r) {
      // 完全にクエリ範囲内
      return this.tree[node];
    }
    // 部分的にクエリ範囲に重なる場合
    const mid = Math.floor((start + end) / 2);
    const leftQuery = this.query(2 * node + 1, start, mid, l, r);
    const rightQuery = this.query(2 * node + 2, mid + 1, end, l, r);
    return Math.max(leftQuery, rightQuery);
  }

  // 範囲クエリを簡単に呼び出す
  public rangeQuery(l: number, r: number): number {
    return this.query(0, 0, this.n - 1, l, r);
  }

  // 値の更新
  private update(node: number, start: number, end: number, idx: number, value: number): void {
    if (start === end) {
      // 葉ノードの更新
      this.tree[node] = value;
    } else {
      const mid = Math.floor((start + end) / 2);
      const leftNode = 2 * node + 1;
      const rightNode = 2 * node + 2;

      if (idx <= mid) {
        // 左の子ノードを更新
        this.update(leftNode, start, mid, idx, value);
      } else {
        // 右の子ノードを更新
        this.update(rightNode, mid + 1, end, idx, value);
      }

      // 内部ノードの更新
      this.tree[node] = Math.max(this.tree[leftNode], this.tree[rightNode]);
    }
  }

  // 値の更新を簡単に呼び出す
  public pointUpdate(idx: number, value: number): void {
    this.update(0, 0, this.n - 1, idx, value);
  }
}
```

#### **使用例**
```typescript
const arr: number[] = [1, 3, 5, 7, 9, 11];
const segTree = new SegmentTree(arr);

// 範囲 [1, 4] の最大値を取得
console.log(segTree.rangeQuery(1, 4)); // 出力: 9

// 配列の 3 番目の値を 6 に更新
segTree.pointUpdate(3, 6);

// 更新後の範囲 [1, 4] の最大値を取得
console.log(segTree.rangeQuery(1, 4)); // 出力: 9
```

---

### **まとめ**
- **セグメントツリーの基本操作**: 範囲クエリとポイント更新をサポート。
- **効率性**: 構築 \(O(N)\)、クエリ・更新 \(O(\log N)\)。
