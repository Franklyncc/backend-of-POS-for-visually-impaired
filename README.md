# backend-of-POS-for-visually-impaired
The official document of the point-of-sale system for the visually impaired designed for 甜裡開始

## API
### session
* POST pos/users/

    * Function: Get user list
    * Request Format: form data
    * Parameter:
        token: "some secret"
    * Example: 
    
        ```
        $.post( "pos/users/", { token: "some secret"}, function(data){console.log(data);});
        ```
    
    * Return example:
        
        ```
        {"users": ["guest"]}
        ```
    
* POST pos/signin/
    * Function: Employee/administrator login
    * Request Format: form data
    * Parameter:
        username: var string
        password: var string
    * Example: 
        ```
        $.post( "pos/signin/", { username: "guest", password: "aabbaabbaabb" }, function(data){console.log(data);} );
        

        // on success:
        {"success": "true"}
        // on failure
        {"success": "false"}
        ```
    
* GET pos/signout/
    * Function: Employee/administrator logout
    * Parameter: None

### queue management
* GET pos/queue/
    * Function: Get orders in queue
    * Parameter: None
    * Return format:JSON
    
        Returned order queue will be FIFO

    * Return example:
        ```
        {"queue":
            // one entry per order
        [
            {"table": "1",            // table ID，string(1~4)
            "category": "\u706b\u934b",     // product type
            "name": "Noodle",         // product name
            "pub_date": "2017-04-09T12:24:48.968Z",  // date of delivery
            "number": 2,             // # of products，int
            "id": 2                // order ID，int
            },
            {"table": "1", "category": "\u706b\u934b", "name": "Noodle", "pub_date": "2017-04-09T12:55:07.491Z", "number": 3, "id": 3},
            {"table": "1", "category": "\u706b\u934b", "name": "Rice", "pub_date": "2017-04-09T12:55:08.075Z", "number": 5, "id": 4}
            ]
        }
        ```


* POST post/newOrder/
    * Function: create a set of orders (consists of multiple products, one order per product)
    * Request Format:JSON
    * Parameter:
    ```
    {
        table: [table ID，string(1~4)]
        orders:[
            // one entry per product(order)
            {
            name: [product name, string]
            number: [數量，int]
            }
        ]
    }
    ```
    * Example: (in Jquery)
    ```
    $.post( "pos/newOrder/", crossDomain=true, JSON.stringify({ table:'1', orders:[{'name':'Rice','number':1}] }), function(data){console.log(data);} );
    
    // multiple entries
    $.post( "pos/newOrder/", JSON.stringify({ table:'1', orders:[{'name':'Noodle','number':3},{'name':'Rice','number':5}] }), function(data){console.log(data);} );
    ```

* POST pos/completeOrder/
    * Function: Mark an order as completed
    * Request Format: form data
    * Parameters:
        
        id: OrderID，int

    * Example:
    ```
    $.post( "pos/completeOrder/",{id:'1' }, function(data){console.log(data);} );
    ```

* POST pos/deleteOrder/
    * Function: Delete an order (for correction)
    * Request Format: form data
    * Parameters:
        id: OrderID，int
    * Example: (in jQuery)
    ```
    $.post( "pos/deleteOrder/",{id:4 }, function(data){console.log(data);} );
    ```



### product management
* GET pos/product/
    * Function: Get list of all products
    * Parameters: None
    * Return example:
        ```
        {"products": [{"category": "\u706b\u934b", "name": "Rice", "prize": 10}, {"category": "\u706b\u934b", "name": "Noodle", "prize": 20}]}
        ```

* GET pos/report/day/ pos/report/week/ pos/report/month/
    * Function: Get simple summary of the current day/week/month
    * Parameters: None (administrative user login required)
    * Return example: 

        ```
        {
            "categoryCount": {"\u706b\u934b": 6},       # number of product sold of each category
            "categorySubtotal": {"\u706b\u934b": 270},     # subtotal of each product
            "productCount": {"Rice": 3, "Noodle": 3},     # number of product sold of each product
            "orders": [                    # detail of all orders
                # one entry per order    (the format is slightly different from previous API)
                {
                "number": 1, 
                "table": "1", 
                "name": "Rice", 
                "subtotal": 10, 
                "id": 1, 
                "category": "\u706b\u934b", 
                "unitPrice": 10, 
                "pub_date": "2017-04-09T10:42:15.236Z"
                }, 
                {"number": 2, "table": "1", "name": "Noodle", "subtotal": 40, "id": 2, "category": "\u706b\u934b", "unitPrice": 20, "pub_date": "2017-04-09T12:24:48.968Z"}, {"number": 3, "table": "1", "name": "Noodle", "subtotal": 60, "id": 3, "category": "\u706b\u934b", "unitPrice": 20, "pub_date": "2017-04-09T12:55:07.491Z"}, {"number": 5, "table": "1", "name": "Rice", "subtotal": 50, "id": 4, "category": "\u706b\u934b", "unitPrice": 10, "pub_date": "2017-04-09T12:55:08.075Z"}, {"number": 3, "table": "1", "name": "Noodle", "subtotal": 60, "id": 5, "category": "\u706b\u934b", "unitPrice": 20, "pub_date": "2017-04-09T12:58:23.714Z"}, {"number": 5, "table": "1", "name": "Rice", "subtotal": 50, "id": 6, "category": "\u706b\u934b", "unitPrice": 10, "pub_date": "2017-04-09T12:58:24.286Z"}
            ],
            "productSubtotal": {"Rice": 110, "Noodle": 160},      # subtotal of each product
            "total": {"revenue": 270}                #total revenue (and expenditure???)
        }
        ```
