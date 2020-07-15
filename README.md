# Booking_copy
Created with CodeSandbox

## Purpose
This application can quickly caculate booking price and create booking detail to hostel owner. 
It can also modify an order, delete an order and create a new order.


## Vue Components

### ControlTable
This component is used in render whole table.
use `v-for` to  render each order.
Also add a checkbox in front of every order, make every order editable and can be delete. 

* Demo

![](https://i.imgur.com/kbxlS1O.png)


### ApplyButton
This component is made by `modify button` and `cancel button`.
* Modify Button
Check a checkbox then you can modify each order's price and note. Then click `modify button` will send axios request to server side.
* Cancel Button
Once you check any checkbox, `cancel button` will light up, click it then every change in orderlist will be recover.

* Demo

![](https://i.imgur.com/KFV1Q5s.png)



### ModalDialog
This component is used for add a new booking detail to the list.

* Demo

![](https://i.imgur.com/dGJ0Yxz.png)

### CopyDialog
This component is used for generate booking detail price and accommodations regulation.
And you can press `copy` to copy whole text display above it.

* Demo

![](https://i.imgur.com/wqiffvs.png)
