# Core Concepts

앱의 상태가 평범한 오브젝트로 되어 있다고 해봅시다. 예를 들어, todo 앱의 상태는 다음과 같을 것입니다.:

```js
{
  todos: [{
    text: 'Eat food',
    completed: true
  }, {
    text: 'Exercise',
    completed: false
  }],
  visibilityFilter: 'SHOW_COMPLETED'
}
```

이 오브젝트는 setter가 없다는 것을 제외하고는 “model”과 같습니다. 상태(state)를 임의로 변경할수 없게 하여 버그를 방지하기 위함입니다.

상태(state)의 어떤 것을 바꾸기 위해서는, 액션(action)을 보내야(dispatch) 해야 합니다. 액션(action)은 무엇이 일어났는지 기술되어 있는 평범한 자바스크립트 객체(plain JavaScript object) 입니다. 액션의 몇가지 예는 다음과 같습니다.:

```js
{ type: 'ADD_TODO', text: 'Go to swimming pool' }
{ type: 'TOGGLE_TODO', index: 1 }
{ type: 'SET_VISIBILITY_FILTER', filter: 'SHOW_ALL' }
```

모든 변경을 액션으로 기술하면 앱에서 어떤 일이 일어나고 있는지 명확히 알 수 있습니다. 무언가가 변했다면, 왜 그것이 변했는지 알수 있습니다. 액션은 변경에 대한 빵부스러기(breadcrumbs)와 같습니다.
마지막으로, 상태(state)와 액션(action)을 연결하기 위해, 리듀서(reducer)라는 함수를 작성합니다. 다시 말하지만 어떤 마술도 없습니다- 리듀서는 상태(state)와 액션(action)을 인자로 받고 앱의 다음 상태(state)를 돌려주는 단순한 함수 입니다. 
거창한 앱을 위한 함수를 작성하는 것은 힘드니, 상태의 일부를 관리하는 작은 함수를 작성해 봅시다.:

```js
function visibilityFilter(state = 'SHOW_ALL', action) {
  if (action.type === 'SET_VISIBILITY_FILTER') {
    return action.filter
  } else {
    return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([{ text: action.text, completed: false }])
    case 'TOGGLE_TODO':
      return state.map(
        (todo, index) =>
          action.index === index
            ? { text: todo.text, completed: !todo.completed }
            : todo
      )
    default:
      return state
  }
}
```

그리고 상태의 키에 해당하는 두개의 리듀서를 호출하여 앱의 상태 전체를 관리하는 다른 리듀서(reducer)를 작성해봅시다.:

```js
function todoApp(state = {}, action) {
  return {
    todos: todos(state.todos, action),
    visibilityFilter: visibilityFilter(state.visibilityFilter, action)
  }
}
```

이것이 리덕스(Redux)의 기본적인 전체 내용입니다.(This is basically the whole idea of Redux.) 아직 리덕스 API를 하나도 사용하지 않았다는 것을 잊지 마세요. 이 패턴을 위한 몇가지 방법이 제공되고 있지만, 중요한 것(main idea)은 액션 오브젝트의 응답에 맞추어 상태가 어떻게 갱신되는 것인지에 대해 작성한다는 것이고, 작성되는 코드의 90%는 라덕스 자체와 리덕스의 API나 어떠한 마법도 사용하지 않는 단순한 자바스크립트라는 것입니다.
