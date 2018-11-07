# Reducer Creator Documentation

## Prerequisites
* [Lodash](https://lodash.com/docs/4.17.11)

### ReducerCreator
**reducer.creator.ts**
```
import { Action } from '@ngrx/store';
import { invoke, camelCase, hasIn, find } from 'lodash';

export function createReducer(initialState, ...reducers) {
  return (state = initialState, action: Action) => {
    const methodName = camelCase(action.type);
    const currentReducer = find(reducers, (reducer) => {
      return hasIn(reducer, methodName);
    });

    if (!currentReducer) {
      return state;
    }

    return invoke(currentReducer, methodName, state, action);
  };
}
```

### Reducer instance
**my-reducer.ts**
```
export const curriedReducer = createReducer(
  initialState,
  ...reducerList
);

export function myReducer
  (state: myReducerState = initialState, action: Action) {
  return curriedReducer(state, action);
}
```

```...reducerList``` represents the array of accepted nested reducers that are executed inside the main reducer, which is registered at the application level. All nested reducers operate on ```myReducerState```, the same fragment of the application state.

### Registering reducers at application level
**app.module.ts**
```
@NgModule({
  declarations: [AppComponent],
  imports: [
    StoreModule.provideStore({
      myReducer: myReducer,
    }),
   ....
  ],
  providers: [...],
  exports: [],
  bootstrap: [AppComponent]})
  ```