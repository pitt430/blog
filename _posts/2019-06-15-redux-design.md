---
layout: post
title: React Redux State Evaluation
date: 2019-06-15
Author: peterli
tags: [React, Redux]
comments: true
toc: true
pinned: true
---

## Introduction

When I started using Redux and React-Redux, it feels like just following the designed pattern or template to fill in the code.
But still confused why do we need to write like this, why do we need mapStateToProps and mapDispatchToProps, what are the purposes of the design?
If you don't have a understanding of the background concepts, it's slower to pick up the concepts.
This article is to illustrate the evaluation of React Redux step by step through some samples.

### Parent-Children communication

This sample will not use any state management, a demo of To-do list.

- Parent pass info to children through props
- Children pass info to parent through callback functions

TodoForm: Get a callback function 'addTodoItem' from parent

```js
const TodoForm = ({ addTodoItem }) => {
  const [value, setValue] = useState("");

  const handleSubmit = e => {
    e.preventDefault();
    if (!value) return;
    addTodoItem({ text: value, isDone: false });
    setValue("");
  };

  return (
    <div>
      <div>
        <form onSubmit={handleSubmit}>
          <input
            type="text"
            placeholder="Task"
            onChange={e => setValue(e.target.value)}
            value={value}
          />
          <button type="submit">Add Task</button>
        </form>
      </div>
    </div>
  );
};
```

TodoList: Get todos as input props to bind the todo list. 'removeTask' and 'completeTask' are callback functions passed in from parent.

```js
const TodoList = ({ todos, removeTask, completeTask }) => {
  return (
    <div>
      {Array.from(todos).map((todo, index) => (
        <TodoListItem
          key={index}
          index={index}
          todo={todo}
          removeItem={removeTask}
          itemDone={completeTask}
        />
      ))}
    </div>
  );
};
```

This is the parent component to pass the todos and callback functions to children component through props.

```js
const App = () => {
  const [todos, setTodos] = useState([
    { text: "learn react", isDone: false },
    { text: "finish blog ", isDone: true }
  ]);
  //When we declare a state variable with useState, it returns a pair — an array with two items. The first item is the current value, and the second is a function that lets us update it.

  const addItem = todoItem => {
    const newTodos = [...todos, todoItem];
    setTodos(newTodos);
  };
  const removeTask = index => {
    const newTodos = [...todos];
    newTodos.splice(index, 1);
    setTodos(newTodos);
  };

  const completeTask = index => {
    const newTodos = [...todos];
    newTodos[index].isDone = true;
    setTodos(newTodos);
  };
  return (
    <div>
      <TodoForm addTodoItem={addItem} />
      <TodoList
        todos={todos}
        removeTask={removeTask}
        completeTask={completeTask}
      />
    </div>
  );
};
```
