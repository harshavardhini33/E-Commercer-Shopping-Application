# EXP 26: E-COMMERCE SHOPPING APPLICATION

## AIM:
 To create a E-commerce Shopping application using React,SpringBoot and SQL.

 ## ALGORITHM:

 1. Create a SpringBoot project with the required specifications.
 
 2. Set up the JDK environment and add the necessary java files.
 
 3. Create a suitable database in PostgresSQL and connect it to your SpringBoot File
 
 4. Create the User Interface using React and define the specific components.
 
 5. Connect the front end to the back end.
 
 6. Run the project and make necessary changes as required.

 ## PROGRAM:

 ### SPRING BOOT:

 ### ECommerce.java:
 java
package com.example.saveetha.Shopping.ECom;
import jakarta.persistence.*;
@Entity
@Table
public class ECommerce {
    @Id
    @SequenceGenerator(
            name = "product_sequence",
            sequenceName = "product_sequence",
            allocationSize = 1
    )
    @GeneratedValue(
            strategy = GenerationType.SEQUENCE,
            generator = "product_sequence"
    )
    private Long productId;
    private String productName;
    private Integer productPrice;
    private Integer productQuantity;

    public ECommerce() {
    }

    public ECommerce(Long productId, String productName, Integer productPrice, Integer productQuantity) {
        this.productId = productId;
        this.productName = productName;
        this.productPrice = productPrice;
        this.productQuantity = productQuantity;
    }
    public Long getProductId() {return productId;}
    public void setProductId(Long productId) {this.productId = productId;}
    public String getProductName() {return productName;}
    public void setProductName(String productName) {this.productName = productName;}
    public Integer getProductPrice() {return productPrice;}
    public void setProductPrice(Integer productPrice) {this.productPrice = productPrice;}
    public Integer getProductQuantity() {return productQuantity;}
    public void setProductQuantity(Integer productQuantity) {this.productQuantity = productQuantity;}

    @Override
    public String toString() {
        return "ECommerce{" +
                "productId=" + productId +
                ", productName='" + productName + '\'' +
                ", productPrice=" + productPrice +
                ", productQuantity=" + productQuantity +
                '}';
    }
}



### ECommerceController.java:
java
package com.example.saveetha.Shopping.ECom;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping(path = "api/v1/shopping")
public class ECommerceController {
    private final ECommerceService eCommerceService;
    @Autowired
    public ECommerceController(ECommerceService eCommerceService){this.eCommerceService = eCommerceService;}
    @GetMapping("/")
    public List<ECommerce> getProductDetails(){return eCommerceService.displayProductDetails();}
    @PostMapping("/")
    public void postProductDetails(@RequestBody ECommerce eCommerce){ECommerceService.insertNewProduct(eCommerce);}
    @DeleteMapping(path = {"/{id}"})
    public void deleteProduct(@PathVariable("id") Long productId){ eCommerceService.removeProduct(productId);}
}


### REACT CODES:
### App.js:
js
import React from 'react';
import './App.css';
import { BrowserRouter as Router, Route, Routes, Link } from "react-router-dom";
import ProductDirectoryComponent from './components/ProductDirectoryComponent/ProductDirectoryComponent';
import ProductAddComponent from './components/ProductAddComponent/ProductAddComponent';
import ProductDeletionComponent from './components/ProductDeletionComponent/ProductDeletionComponent';
import HeaderComponent from './components/HeaderComponent/HeaderComponent';
function App() {
  return (
    <Router>
      <div className='container'>
        <center>
        <HeaderComponent/>
        </center>
      
      <nav className='nav-menu'>
        <Link to='/'>Shopping Cart</Link>
        <Link to='/admin/add'>Add Products</Link>
        <Link to='/admin/delete'>Delete Items</Link>
      </nav>
      <Routes>
        <Route exact path = '/' element={<ProductDirectoryComponent/>}></Route>
        <Route path = '/admin/add' element={<ProductAddComponent/>}></Route>
        <Route path = '/admin/delete' element={<ProductDeletionComponent/>}></Route>
      </Routes>
      </div>
    </Router>
  );
}
export default App;


### ProductDirectoryComponent.js:
js
import React, { useEffect, useState } from 'react';
import './ProductDirectoryComponent.css'; 

function ProductDirectoryComponent() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    fetchProducts();
  }, []);

  const fetchProducts = async () => {
    try {
      const response = await fetch('http://localhost:8080/api/v1/shopping/');
      const data = await response.json();
      setProducts(data);
    } catch (error) {
      console.error('Error:', error);
    }
  };

  return (
    <div className="product-list">
      {products.map((product) => (
        <div className="product-card" key={product.productID}>
          <p>Product ID: {product.productID}</p>
          <p>Product Name: {product.productName}</p>
          <p>Product Quantity: {product.productQuantity}</p>
          <p>Product Price: {product.productPrice}</p>
        </div>
      ))}
    </div>
  );
};

export default ProductDirectoryComponent;


### ProductAddComponent.js:
js
import React, { useState } from "react";
import './ProductAddComponent.css'

function ProductAddComponent() {
    const [product, setProducts] = useState({
        productName: '',
        productQuantity: '',
        productPrice: ''
    });

    const handleChange = (event) => {
        const {name, value} = event.target;
        setProducts({...employee, [name]:value});
    };

    const handleFormSubmit =async (event) => {
        event.preventDefault();
        await fetch('http://localhost:8080/api/v1/shopping/', {
            method: 'POST',
            headers: {
                'Content-type': 'application/json'
            },
            body: JSON.stringify(product)
        })

        .then((response) => {
            if (response.status == 500)
            {
                alert(`Error!`)
            }
            else{
                alert(`Data of ${product.productName} is added successfully`)
                window.location.href = '/'
            }
        })
  };
            
    return(
        <form onSubmit={handleFormSubmit}>
            <label>
                Product Name: 
                <input type="text" name="productName" value={product.productName} onChange={handleChange} required/>
            </label> 
            <label>
                Product Quantity: 
                <input type="text" name="productQuantity" value={product.productQuantity} onChange={handleChange} required/>
            </label> 
            <label>
                Product Price: 
                <input type="text" name="productPrice" value={product.productPrice} onChange={handleChange} required/>
            </label>

            <button type="submit">SUBMIT</button> 
        </form>
    )
}

export default ProductAddComponent;


### ProductDeletionComponent.js:
js
import React, { useState } from 'react';
import './ProductDeletionComponent.css'
function ProductDeletionComponent() {
  const [productID, setProductID] = useState('');

  const handleChange = (event) => {
    setProductID(event.target.value);
  };
  const handleSubmit = async(event) => {
    event.preventDefault();
    await fetch(`http://localhost:8080/api/v1/shopping/${productID}`,{
      method: 'DELETE'
    })
    .then((response) => {
            if (response.status == 500)
            {
                alert(`Error!`)
            }
            else{
                alert(`Data deleted successfully`)
                window.location.href = '/'
            }
        })
  };
  return (
    <form onSubmit={handleSubmit}>
      <label>
        Product ID:
        <input
          type="number"
          name="productID"
          value={productID}
          onChange={handleChange}
          required
        />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
};

export default ProductDeletionComponent;




## OUTPUT:
![image](https://github.com/Aashima02/ECommerce-Shopping-Application/assets/93427086/e2b0d5c8-85be-48e3-b8f5-feb9f50ff16c)

![image](https://github.com/Aashima02/ECommerce-Shopping-Application/assets/93427086/dc5b51b4-2110-47d4-bf09-4fb3746065a9)



## RESULT:
Thus, a E-commerce Shopping application is created using React, springboot and SQL
