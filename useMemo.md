在 react 中性能优化点有：

调用 setState，就会触发组件的重新渲染，无论前后的 state 是否不同
父组件更新，子组件也会自动的更新
在 hooks 之前的类组件书写中，我们通过在 shouldComponentUpdate 声明周期中判断前后的 props 和 state，如果没有变化，则返回 false 来阻止更新，hooks 出来之后我们使用 fucntion 函数式书写创建包含内部 state 状态的组件，不再存在 shouldComponentUpdate，此时无法再通过前后状态的变化来判断是否更新，在函数式组件中 useEffect 综合部分声明周期，每一次调用都会执行其内部的所有逻辑，为了解决上述的问题，避免一定的性能消耗，我们使用 useMemo 来进行处理。

```js
import React from "react";
export default function WithoutMemo() {
  const [count, setCount] = useState(1);
  const [val, setVal] = useState("zyf");
  function modifiy() {
    console.log("int");
    let sum = 0;
    for (let i = 0; i < count * 10; i++) {
      sum += i;
    }
    return sum;
  }
  return (
    <div>
      <h3>
        {count}-{val}-{modifiy()}
      </h3>
      <div>
        <button onClick={() => setCount(count + 1)}></button>
        <input type="text" val={val} onChange={(e) => setVal(e.target.value)} />
      </div>
    </div>
  );
}
```

上面例子中，未使用 useMemo,我们创建了两个 state,每一次修改 count 或者 val 都会重新渲染，每次渲染都会执行 modifiy 函数，正常 val 的变化不应该执行该函数，因此就造成很大的性能消耗，此刻就需要使用 useMemo 来避免这类问题的出现：

```js
import React from "react";
export default function WithMemo() {
  const [count, setCount] = useState(1);
  const [val, setVal] = useState("zyf");
  const modifiy = useMemo(() => {
    console.log("int");
    let sum = 0;
    for (let i = 0; i < count * 10; i++) {
      sum += i;
    }
    return sum;
  }, [count]);
  return (
    <div>
      <h3>
        {count}-{val}-{modifiy()}
      </h3>
      <div>
        <button onClick={() => setCount(count + 1)}></button>
        <input type="text" val={val} onChange={(e) => setVal(e.target.value)} />
      </div>
    </div>
  );
}
```
