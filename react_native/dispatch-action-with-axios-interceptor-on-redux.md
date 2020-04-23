# Dispatch action with axios interceptor on redux

Dispatch an action on interceptor makes it possible to treat a response and dispatch some action,
for example: if status response is unauthorized, dispatch SIGN_OUT action. (in this case, 401 is invalid token)

Start creating a registerInterceptWithStore function on ~/services/api.js

## ~/services/api.js

```javascript
import axios from 'axios';

import { signOut } from '../store/modules/auth/actions';

const api = axios.create({
  baseURL: 'https://localhost:3333/',
  withCredentials: false,
});

api.registerInterceptWithStore = (store) => {
  api.interceptors.response.use(
    api.interceptors.response.use(
      (response) => response,
      (err) => {
        if (err.response.status === 401) {
          store.dispatch(signOut());
        }
        return Promise.reject(err);
      },
    ),
  );
};

export default api;
```

and call this function before export store from redux config

## ~/store/index.js

```javascript
import api from '../services/api';
......

api.registerInterceptWithStore(store);

export { store };
```
