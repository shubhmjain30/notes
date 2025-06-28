# Express.js Notes

This folder contains comprehensive Express.js learning materials, covering routing, middleware, RESTful API development, and production deployment strategies.

## **Learning Path**

**Start Here:** [[express-roadmap|Express.js Mastery Roadmap]]

## **Core Topics**

1. **Foundation**
   - [[express-intro|Express.js Introduction]] - Core concepts and setup
   - [[express-routing-middleware|Routing & Middleware]] - Request handling and middleware patterns

2. **Building APIs**
   - [[express-restful-api|RESTful API Design]] - Building robust API endpoints
   - [[express-error-handling|Error Handling]] - Graceful error management strategies

3. **Advanced Usage**
   - [[express-advanced-features|Advanced Features]] - Performance, security, and scalability
   
## **Quick Reference**

- **Setup a basic Express server:**
  ```javascript
  const express = require('express');
  const app = express();
  const port = process.env.PORT || 3000;

  app.get('/', (req, res) => {
    res.send('Hello World!');
  });

  app.listen(port, () => {
    console.log(`Server running on port ${port}`);
  });
  ```

- **Core middleware:** Express.json(), Express.urlencoded(), Express.static()
- **Error handling:** Custom error middleware with next(error)
- **Route parameters:** /users/:id with req.params.id

---

**Next Steps:** After mastering Express.js, consider exploring database integration with MongoDB or PostgreSQL.
