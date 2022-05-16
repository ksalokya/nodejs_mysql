# Nodejs MySQL CRUD

```js
const express = require("express");
const mysql = require("mysql");
const bodyParser = require("body-parser");

const port = process.env.PORT || 5000;
const app = express();

app.use(bodyParser.urlencoded({extended: true}));
app.use(bodyParser.json());

const mysqlConnection = mysql.createConnection({
    host: "localhost",
    user: "root",
    password: "root",
    database: "employeeDB",
    multipleStatements: true
});

mysqlConnection.connect((err) => {
    if (!err)
        console.log('DB connection succeeded.');
    else
        console.log('DB connection failed \n Error : ' + JSON.stringify(err, undefined, 1));
});

app.get("/", function (req, res) {
    res.send("Node.js, Express.js and MySQL CRUD")
});


// Insert an employee
app.post('/employees', (req, res) => {
    let emp = req.body;
    mysqlConnection.query('INSERT INTO Employee VALUES (?, ?, ?)', [emp.id, emp.name, emp.email], (err, rows, fields) => {
        if (!err) {
            res.send('Inserted successfully.');
        } else {
            console.log(err)
        }
    })
});

// Get all employees
app.get('/employees', (req, res) => {
    mysqlConnection.query('SELECT * FROM Employee', (err, rows, fields) => {
        if (!err)
            res.send(rows);
        else
            console.log(err);
    })
});

// Get an employees
app.get('/employees/:id', (req, res) => {
    mysqlConnection.query('SELECT * FROM Employee WHERE EmployeeID = ?', [req.params.id], (err, rows, fields) => {
        if (!err)
            res.send(rows);
        else
            console.log(err);
    })
});

// Update an employee
app.put('/employees', (req, res) => {
    let emp = req.body;
    mysqlConnection.query('UPDATE Employee set Name = ?, Email = ? where EmployeeID = ?', [emp.name, emp.email, emp.id], (err, rows, fields) => {
        if (!err)
            res.send('Updated successfully');
        else
            console.log(err);
    })
});

// Update employee's particular field
app.patch('/employees', (req, res) => {
    let emp = req.body;
    let myMap = new Map(Object.entries(emp));

    // preparing query
    let query = 'UPDATE Employee set ';
    let values = [];
    let flag = false;

    myMap.forEach((value, key) => {
        if (key !== 'id') {
            // query string
            let tempStr = key + " = ?";
            if(flag){
                tempStr = ", " + tempStr;
            }
            query += tempStr;
            flag = true;

            // values array
            values.push(value);
        }
    })

    query += " where EmployeeID = ?";
    values.push(emp.id);

    mysqlConnection.query(query, values, (err, rows, fields) => {
        if (!err)
            res.send('Updated successfully');
        else
            console.log(err);
    })
})

// Delete an employee
app.delete('/employees/:id', (req, res) => {
    let emp = req.body;
    mysqlConnection.query('DELETE FROM Employee WHERE EmployeeID = ?', [emp.id], (err, rows, fields) => {
        if (!err)
            res.send('Deleted successfully.');
        else
            console.log(err);
    })
});

app.get("*", function (req, res) {
    res.redirect("/");
});

app.listen(port, function () {
    console.log(`Serve at http://localhost:${port}`);
});
```

# Dependencies
```js
  "dependencies": {
    "body-parser": "^1.20.0",
    "express": "^4.18.1",
    "mysql": "^2.18.1"
  }
```
