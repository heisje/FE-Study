# Redux 사용시 미들웨어 사용 이유

```
주로 비동기처리를 하기 위해 사용합니다.

리덕스의 Flux 패턴에서 맨 처음 액션을 디스패치 하게 되면, 리듀서에서 그 해당 액션에 대한 정보를 바탕으로 스토어의 상태값을 바꾸게 되는데, 이때 미들웨어를 사용하면 액션이 스토어에서 상태값을 바꾸기 전에 특정 작업들을 수행할 수 있게 됩니다. 해당 액션이 제때 이루어질 수 있도록 비동기처리를 하기 위해 미들웨어를 사용합니다.
```



### Redux Thunk와 Redux Saga

**사용하는 상황**

- Redux Thunk
  - 보일러플레이트 코드가 적고 이해하기 쉽기 때문에 서비스 로직이 작거나 규모가 작은 경우 적합
  - 하지만 잘못 사용할 경우 많은 async 로직을 구현하게 되어 비동기 로직이 복잡해질 수 있고, 테스트 하기 어려운 구조로 되어 있어 unit test를 자주 하는 환경에서는 적용하기 어려움
- Redux Saga
  - 보일러플레이트 양이 많고, 제너레이터 등의 개념 때문에 러닝 커브가 높음
  - 하지만 Thunk에 비해 프로젝트 규모를 키우기 용이하고, 여러 Saga의 effects들을 활용하면 Thunk에 비해 깔끔한 로직을 구현 가능
  - Throttling, Debouncing, API 재요청 및 취소와 관련한 로직 구현이 용이하기 때문에 Thunk에 비해 활용도가 높음



**Redux Thunk**

- Redux는 기본적으로 액션객체만을 디스패치 가능
- 하지만 Redux Thunk를 사용하면 객체 대신 함수를 생성하는 액션 생성함수를 작성 가능
- 이러한 동작방식을 활용하여 Redux에서 비동기적인 프로그래밍을 구현 가능

```tsx
import axios from "axios";
import { createAsyncThunk, createSlice } from "@reduxjs/toolkit";

const fetchData = createAsyncThunk("FETCH_DATA", async () => {
  try {
    const response = await axios.get("<http://localhost:8080>");
    return response.data;
  } catch (err) {
    console.error(err);
  }
});

export const rootReducer = createSlice({
  name: "Data",
  initialState: { data: [] },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchData.pending, (state, action) => {})  // 통신 대기중
      .addCase(fetchData.fulfilled, (state, action) => {
        return { ...state, data: [ ...action.payload ]}   // 성공시
      })
      .addCase(fetchData.reject, (state, action) => {})   // 실패시
  },
});

// app.ts
dispatch(fetchData());
```

- 비동기적으로 서버에서 데이터를 불러와 활용할 수 있는 기본적인 구조의 액션 함수



**Redux Saga**

- Thunk는 함수를 디스패치 할 수 있게 해주는 미들웨어라면, Saga는 액션을 모니터링 하고 있다가 특정 액션이 발생했을 때, 미리 정해둔 로직에 따라 특정 작업이 이루어지는 방식

- Sagas라는 순수함수들로 로직을 처리 가능

  - 순수함수로 이루어지다보니, 사이드 이펙트도 적고 테스트 코드를 작성하기에도 용이

- Thunk에 비해 많은 기능 수행 가능

  - 비동기 작업 진행시, 기존 요청 취소 가능
  - 특정 액션이 발생했을 때, 이를 구독하고 있다가 다른 액션을 디스패치 하거나 특정 자바스크립트 코드를 실행 가능
  - 웹소켓 사용시, 더 효율적인 코드 관리 가능
  - API 요청 실패시 재요청 가능

- 제너레이터(Generator)라는 특수한 형태의 함수로 구현됨 (제너레이터 함수 : 함수의 실행을 특정 구간에 멈추게 하거나 원하는 시점으로 돌아가게 할 수 있고, 결과값을 여러번 리턴하게도 할 수 있음)

- Redux Saga Effects

  - ```
    all
    ```

    - 제너레이터 함수들이 들어있는 배열을 인자로 받음
    - all effect 안에서 병렬적으로 기능을 수행하며, 이 함수들이 모두 resolve 될 때까지 기다림

  - ```
    call
    ```

    - 함수를 실행시키는 effect
    - effect의 첫번째 인자에는 함수를 넣고, optional로 나머지 인자에 해당 함수에 넣을 인자를 줄 수 있음

  - ```
    fork
    ```

    - 함수를 실행시키는 effect
    - call 과의 차이점은 fork의 경우 함수를 비동기 실행하며, call의 경우는 함수를 동기 실행한다는 점
    - 순차적으로 함수가 실행되어야 하는 api 요청 함수 등의 경우에는 call 사용, 그 외의 비동기 로직에는 fork 사용

  - ```
    put
    ```

    - 특정 액션을 dispatch하는 effect
    - 제너레이터 함수 내부에서 특정 액션을 dispatch

  - ```
    takeEvery / takeLatest
    ```

    - 인자로 들어온 액션에 대해 특정 로직을 실행시켜주는 effect
      - `takeEvery` : 인자로 들어오는 모든 액션에 대해 로직 실행
      - `takeLatest` : 기존에 실행중이던 작업이 있을 경우 이를 취소하고, 가장 마지막으로 실행된 작업만 실행

```tsx
// store.ts
import { configureStore } from "@reduxjs/toolkit";
import { useDispatch } from "react-redux";
import { rootReducer } from "~store/rootReducer";
import sagaMiddleware, { rootSaga } from "~store/rootSaga";

export const store = configureStore({
  reducer: rootReducer,
  middleware: [sagaMiddleware]  // sagaMiddleware를 configureStore에 등록
});

// rootSaga를 실행
sagaMiddleware.run(rootSaga);

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;

export const useAppDispatch = () => useDispatch<AppDispatch>();

export default store;
```

- 위에서 만든 sagaMiddleware를 Store에 등록

```tsx
// rootSaga.ts
import createSagaMiddleware from "redux-saga";
import { all, call } from "redux-saga/effects";
import watchGetData from "~store/fetchDataSaga";

// sagaMiddleware를 생성
const sagaMiddleware = createSagaMiddleware();

// 모든 saga들을 합치는 rootSaga를 만듦
// 여러 Saga들을 하나로 합칠 때에는 all()의 인자로 들어있는 배열에 saga들을 넣어주면됨
export function* rootSaga() {
  yield all([call(watchGetData)]);
}

export default sagaMiddleware;
```

- saga 사용

```tsx
// fetchDataSage.ts
import { fetchDataActions } from "./fetchDataSlice";
import { all, fork, call, put, takeLatest } from "redux-saga/effects";
import axios, { AxiosResponse } from "axios";

// 외부 데이터를 불러오는 함수입니다.
// 코드의 가독성을 위해 다른 파일로 분류하는 것이 더 좋지만,
// 설명의 편의성을 위해 한 saga 파일에 포함시켰습니다.
const fetch = () => {
  return axios.get(
    "<http://localhost:8080>"
  );
};

function* fetchData() {
  try {
    const response: AxiosResponse = yield call(fetch);
    yield put(fetchDataActions.getDataSuccess(response.data.articles));
  } catch (error) {
    console.error(error);
    yield put(fetchDataActions.getDataError(error));
  }
}

// getData 액션을 감지하는 함수를 작성합니다.
// 해당 함수는 getData 액션을 감지하고 있다가,
// 액션이 실행되면, 두번째 인자로 들어있는 제너레이터 함수를 실행합니다.
function* watchGetData() {
  yield takeLatest(fetchDataActions.getData, fetchData);
}

export default watchGetData;
// fetchDataSlice.ts
import { createSlice } from "@reduxjs/toolkit";

export interface DataInterface {
  id: string;
  title: string;
}

export interface StateInterface {
  isLoading: boolean;
  data: DataInterface[];
  error: boolean;
}

const initialState: StateInterface = {
  isLoading: false,
  data: [],
  error: false
};

export const dataSlice = createSlice({
  name: "data",
  initialState,
  reducers: {
    // api를 실행하는 액션입니다.
    getData: (state) => {
      state.isLoading = true;
    },
    // api로 데이터를 불러오는데 성공하면 실행되는 액션입니다.
    getDataSuccess: (state, action) => {
      state.isLoading = false;
      state.data = action.payload;
    },
    // api로 데이터를 불러오는데 실패하면 실행되는 액션입니다.
    getDataError: (state, action) => {
      state.isLoading = false;
      state.error = true;
      state.data = action.payload;
    }
  }
});

export const fetchDataActions = dataSlice.actions;
export default dataSlice.reducer;
// index.tsx
import { VFC, useEffect } from "react";
import { fetchDataActions } from "~store/fetchDataSlice";
import { useSelector } from "react-redux";
import { RootState, useAppDispatch } from "~store/configureStore";

const Main: VFC = () => {
  const dispatch = useAppDispatch();
  const state = useSelector((state: RootState) => {
    return state.data;
  });

  // useEffect에서 데이터를 요청하는 액션을 dispatch합니다.
  useEffect(() => {
    dispatch(fetchDataActions.getData());
  }, []);

  return (
    <article>
      <section>
        <p>데이터를 활용하는 페이지입니다.</p>
        <p>{state.isLoading ? "로딩 중" : "로딩 완료"}</p>
        {state.data.map((element, idx) => {
          return <p key={idx}>{element.title}</p>;
        })}
      </section>
    </article>
  );
};

export default Main;
```



### 참고사이트

- https://shinejaram.tistory.com/76