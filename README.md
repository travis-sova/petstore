# Swagger Petstore API Automation Tests

## 🧰 Tools & Frameworks
- **Postman** for request definition and test scripting  

---

## 🔍 Endpoints Covered
| HTTP Method | Endpoint                   | Category      |
|-------------|----------------------------|---------------|
| POST        | `/v2/pet`                  | Functional    |
| GET         | `/v2/pet/{petId}`          | Functional    |
| PUT         | `/v2/pet`                  | Functional    |
| DELETE      | `/v2/pet/{petId}`          | Functional    |
| GET         | `/v2/pet/findByStatus`     | Functional    |
| POST        | `/v2/pet`                  | Negative      |
| GET         | `/v2/pet/{invalid-id}`     | Negative      |
| PUT         | `/v2/pet`                  | Negative      |
| DELETE      | `/v2/pet/{invalid-id}`     | Negative      |
| GET         | `/v2/pet/findByStatus?status=` | Negative  |
| POST        | `/v2/pet`                  | Edge          |
| GET         | `/v2/pet/{huge-id}`        | Edge          |
| PUT         | `/v2/pet`                  | Edge          |
| DELETE      | `/v2/pet/{huge-id}`        | Edge          |
| GET         | `/v2/pet/findByStatus?status=available,sold,pending` | Edge |

---

## ✅ Functional Test Scenarios
1. **Add a new pet**  
   - Verify `200 OK`  
   - Response JSON has keys: `id`, `category`, `name`, `photoUrls`, `tags`, `status`  
   - Category name non-empty string  
   - At least one `photoUrl`  
   - Status is one of `["available","pending","sold"]`  

2. **Get pet by ID**  
   - `200 OK` for existing pet  
   - Same field and type checks as “Add”  
   - Tags array items each have `id` & `name`  

3. **Update pet**  
   - `200 OK`  
   - `id` non-negative integer  
   - `name` non-empty string  
   - At least one valid `photoUrls` entry  

4. **Delete pet**  
   - `200 OK`  
   - Body keys: `code`, `type`, `message`  
   - `code` ≥ 0, `message` is string   

5. **Find pets by status**  
   - `200 OK`  
   - Response is non-empty array of objects  
   - Each object has `id`, `name`, `photoUrls`  
   - Each pet’s `status` is valid  

---

## ❌ Negative Test Scenarios
1. **Add pet with invalid payload**  
   - Expect `500` (server error)  
   - Response JSON keys: `code`, `type`, `message`  
   - `message` non-empty string  

2. **Get pet by invalid ID**  
   - `404 Not Found`  
   - Body keys: `code`, `type`, `message`  
   - `type` equals `"unknown"`  
   - `message` reports `NumberFormatException`  

3. **Update with malformed JSON**  
   - `500`  
   - Standard error fields present  

4. **Delete non-existent pet**  
   - `404`  
   - Body is valid JSON with `code`, `type`, `message`  

5. **Find by empty status**  
   - `200 OK`  
   - Returns array (may be empty)  
   - Validate structure even if input is blank  

---

## ⚠️ Edge-Case Test Scenarios
1. **Add pet with extremely large ID**  
   - ID: `999999999999999999999`  
   - Expect `500`  

2. **Get pet by negative ID**  
   - `404`  
   - `message` = `"Pet not found"`  

3. **Update pet with extra/unexpected fields**  
   - `200 OK`  
   - Response JSON still contains required fields  

4. **Delete pet with huge ID**  
   - `404`  
   - Standard error structure  

5. **Find by status list**  
   - Query: `status=available,sold,pending`  
   - `200 OK`, validate each object  

---

## 📝 Observations & Issues
- **Error handling** is inconsistent: malformed JSON often returns **500** instead of **400**.  
- **Empty or blank** query parameters still return **200 OK**—ideally should validate input and return a client error.  
- **Edge IDs** aren’t strictly validated; very large or negative IDs lead to generic errors.

---

_End of report._  
