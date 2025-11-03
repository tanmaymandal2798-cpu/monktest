# monktest
This is a prototype RestAPI for ecommerce coupon logic 
Features and Limitations
________________________________________
🛍️ Coupon Management REST API
Built with Python Flask + SQL Server (via pyodbc)
This project implements a Coupon Management System backend for an e-commerce platform.
It provides REST APIs to create, update, list, delete, and apply various types of discount coupons such as:
•	Cart-wise coupons
•	Product-wise coupons
•	Buy X Get Y (BXGY) coupons
________________________________________
⚙️ Tech Stack
Component	Technology
Framework	Flask (Python)
Database	SQL Server
DB Connector	pyodbc
Architecture	RESTful API
Data Exchange Format	JSON
________________________________________
🧱 Database Schema
Run the following SQL script in your SQL Server database:
CREATE TABLE coupons ( ID INT IDENTITY(1,1) PRIMARY KEY, type NVARCHAR(50) NOT NULL, discountdetails NVARCHAR(MAX) NOT NULL, condition NVARCHAR(MAX) NOT NULL);

Columns:
Column	Type	Description
ID	INT (PK)	Unique coupon ID
type	NVARCHAR(50)	Type of coupon (cart-wise, product-wise, bxgy)
discountdetails	NVARCHAR(MAX)	JSON string storing discount info
condition	NVARCHAR(MAX)	JSON string defining applicability
________________________________________
📁 Project Structure
project/
│
├── app.py          # Main Flask application (routes & logic)
├── db.py           # SQL Server connection configuration
└── README.md       # Project documentation
________________________________________
⚡ Setup & Run Instructions
1️⃣ Install Dependencies
pip install flask pyodbc
2️⃣ Configure Database
Edit your db.py file:
 
🟢 Run Server
python app.py
The API will start on:
http://127.0.0.1:5000
________________________________________
🧩 API Endpoints
Method	Endpoint	Description
POST	/coupons	Create a new coupon
GET	/coupons	List all coupons
GET	/coupons/<id>	Get coupon by ID
PUT	/coupons/<id>	Update coupon
DELETE	/coupons/<id>	Delete coupon
POST	/applicable-coupons	Find all applicable coupons for a cart
POST	/apply-coupon/<id>	Apply a specific coupon and return updated cart
________________________________________

🧾 JSON Examples

🆕 Create Coupon — POST /coupons

Example 1: Cart-wise
{
  "type": "cart-wise",
  "discountdetails": {
    "discount_percent": 10
  },
  "condition": {
    "min_cart_value": 100
  }
}

Example 2: Product-wise
{
  "type": "product-wise",
  "discountdetails": {
    "discount_percent": 15,
    "product_id": 2
  },
  "condition": {
    "applicable_product": 2
  }
}


Example 3: Buy X Get Y (BXGY)
{
  "type": "bxgy",
  "discountdetails": {
    "buy_products": [
      {"product_id": 1, "quantity": 2}
      ],
    "get_products": [
      {"product_id": 3, "quantity": 1}
    ],
    "repetition_limit": 2
  },
  "condition": {
    "min_cart_value": 0
  }
}
________________________________________
✏️ Update Coupon — PUT /coupons/{id}

{
  "type": "cart-wise",
  "discountdetails": {
    "discount_percent": 20
  },
  "condition": {
    "min_cart_value": 200
  }
}
________________________________________

🔍 Get All Coupons — GET /coupons
[
  {
    "ID": 1,
    "type": "cart-wise",
    "discountdetails": {"discount_percent": 20},
    "condition": {"min_cart_value": 200}
  },
  {
    "ID": 2,
    "type": "bxgy",
    "discountdetails": {
      "buy_products": [{"product_id": 1, "quantity": 2}],
      "get_products": [{"product_id": 3, "quantity": 1}],
      "repetition_limit": 2
    },
    "condition": {"min_cart_value": 0}
  }
]
________________________________________
❌ Delete Coupon — DELETE /coupons/{id}
Response:
{
  "message": "Coupon deleted successfully"
}
________________________________________

🎯 Applicable Coupons — POST /applicable-coupons
Request:
{
  "cart": {
    "items": [
      {"product_id": 1, "quantity": 3, "price": 100},
      {"product_id": 3, "quantity": 1, "price": 50}
    ]
  }
}
Response:
{
  "applicable_coupons": [
    {"ID": 1, "type": "cart-wise", "discount": 45.0},
    {"ID": 3, "type": "bxgy", "discount": 50.0}
  ]
}
________________________________________

💰 Apply Coupon — POST /apply-coupon/{id}
Request:
{
  "cart": {
    "items": [
      {"product_id": 1, "quantity": 3, "price": 100},
      {"product_id": 3, "quantity": 1, "price": 50}
    ]
  }
}

Response Example:
{
  "updated_cart": {
    "items": [
      {"product_id": 1, "quantity": 3, "price": 100, "item_discount": 0.0},
      {"product_id": 3, "quantity": 2, "price": 50, "item_discount": 50.0}
    ],
    "total_price": 350.0,
    "total_discount": 50.0,
    "final_price": 300.0
  }
}
________________________________________


🧮 Coupon Logic Implemented
Coupon Type	Logic
Cart-wise	Applies percentage discount if total cart value > threshold.
Product-wise	Applies percentage discount only to a specific product.
BXGY (Buy X Get Y)	Grants free items based on “buy” and “get” quantities, with repetition limit.
________________________________________

✅ Features
•	Full CRUD API for coupon management
•	JSON-based flexibility — supports dynamic rules without changing schema
•	Cart-wise, Product-wise, BXGY logic implemented
•	Calculate and apply discounts for any cart payload
•	Simple, readable Flask + pyodbc structure
•	Easy to extend with new coupon types
________________________________________
⚠️ Limitations
1.	❌ No authentication or role-based access control
2.	❌ No ORM (manual SQL queries only)
3.	⚠️ Minimal input validation and error handling
4.	🧮 BXGY logic simplified — assumes free items already in the cart
5.	📦 No product or cart persistence (cart data comes from request body)
6.	🧾 No pagination or filtering for /coupons list
7.	🧰 No environment config — credentials stored in db.py
8.	🧪 No automated unit tests yet
________________________________________
🚧 Future Enhancements
•	Add authentication (JWT or API key)
•	Add SQLAlchemy ORM support
•	Add validation using Marshmallow
•	Implement logging and exception middleware
•	Support flat discounts and category-based coupons
•	Add frontend (React/Angular) integration for testing
________________________________________
🧠 Author Notes
•	Designed for backend developer evaluation (Task Monk Commerce 2025).
•	Focuses on correctness, clarity, and extensibility.
•	Compatible with Python 3.8+ and SQL Server 2017+.

