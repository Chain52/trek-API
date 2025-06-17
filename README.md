# Trek API

This repository was intended to serve as the Lambda-based API gateway for the Trek application suite. The goal was to define the HTTP API layer for a modular, serverless backend architecture in this project. Each route would have been implemented as its own AWS Lambda function and designed to remain minimal by delegating all validation, business logic, and error handling to the `trek-API-common` submodule.

> **Status**: Archived  
Although development has ceased, the conceptual architecture outlined in this README demonstrates a clean serverless pattern centered on composability, reuse, and convention over configuration.

---

## Architecture Overview

The API layer was conceptualized with the following goals:

- **Serverless by design**: Each API route handled as a standalone AWS Lambda function.
- **Abstracted Common Logic**: Common code, such as the Express application setup, middleware, and request validation, parsing, and error handling, was to be handled in a separate shared library.
- **Microservice Structure**: Code and responsibilities were designed to grow as independently scalable services through modularity.
- **Express compatibility**: Handlers are written using Express and transformed into Lambda handlers via a helper.

---

## Submodule: Trek-API-common

The API layer relies on [`trek-API-common`](https://github.com/Chain52/trek-API-common) to provide:

- Express handler factory to wrap Lambdas
- Standardized input validation, error handling, and request parsing
- Integration with utility functions (e.g., DB access, logging)

All Lambda handlers in this repository were expected to import and build upon that shared foundation.

---

## Project Structure

At the time of archival, the repository only contains the [`trek-API-common`](https://github.com/Chain52/trek-API-common) submodule.

The intended structure of the API layer is as follows:

```yaml
trek-API-common/     # Submodule containing shared resources
handlers/            # Folder containing nested Lambda route handlers
  user/
    getUser.js
    createUser.js
    ...
  ...
routeRegistry.js     # Registry of route paths
```

### Sample Handler Stub

```mjs
import {
  createReadHandler, // creates an express app wrapped get handler
  getUserById, // validates input and retrieves a user or an error
  getErrorResponse // formats an error response
} from '~/trek-API-common';
import routeRegistry from '~/routeRegistry'

// Utilize object destructuring and a common response framework to handle responses appropriately
// status = HTTP status code, data = the user requested (undefined if error), error = an optional error response
export const getUserHandler = async (req, res) => {
  const { userId } = req.query;
  const { status, data, error } = await getUserById(userId);

  if (data) return res.json(data);
  return getErrorResponse(res, status, error);
}

export const handler = createReadHandler(routeRegistry.getUser, getUserHandler);
```

---

## Status and Reflection

This repository is no longer maintained. Development was paused very early in the process when it became clear the original concept too closely mirrored a prior employer's unfinished product. The effort was halted out of an abundance of caution and respect for intellectual boundaries.

While inactive, the conceptual design remains a reference for future work using:

- Composable serverless functions
- Modular architecture via shared submodules, library, or packages
