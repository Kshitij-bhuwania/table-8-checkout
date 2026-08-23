
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Table 8 - Cart & Total</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; background: #f8f9fa; }
    .box { background: white; border: 1px solid #ddd; padding: 20px; border-radius: 8px; max-width: 450px; margin: 0 auto; }
    ul { list-style: none; padding: 0; }
    li { display: flex; justify-content: space-between; padding: 8px 0; border-bottom: 1px dashed #ccc; }
    .total { font-size: 20px; font-weight: bold; color: #27ae60; text-align: right; margin-top: 15px; }
    .btn-checkout { width: 100%; background: #27ae60; color: white; border: none; padding: 12px; font-size: 18px; font-weight: bold; border-radius: 6px; cursor: pointer; margin-top: 15px; }
    a { display: inline-block; margin-top: 15px; color: #007bff; font-weight: bold; text-decoration: none; }
  </style>
</head>
<body>

  <div class="box">
    <h2>Table 8 - Cart & Total</h2>
    <ul id="cartItems"></ul>
    <div class="total">Total Money: <span id="totalPrice">₹0</span></div>
    <button class="btn-checkout" onclick="checkout()">Checkout Order</button>
    <br>
    <a href="https://kshitij-bhuwania.github.io/table-8/">&larr; Back to Menu</a>
  </div>

  <script>
    const TABLE_NAME = "Table 8";
    const TABLE_KEY = "cart_table_8";
    const channel = new BroadcastChannel("restaurant_orders");

    function renderCart() {
      const cart = JSON.parse(localStorage.getItem(TABLE_KEY)) || [];
      const list = document.getElementById("cartItems");
      let total = 0;
      list.innerHTML = "";
      
      if (cart.length === 0) { 
        list.innerHTML = "<li>No items in cart.</li>"; 
        document.getElementById("totalPrice").textContent = "₹0";
        return; 
      }
      
      cart.forEach(item => {
        total += item.price;
        list.innerHTML += `<li><span>${item.name}</span><strong>₹${item.price}</strong></li>`;
      });
      document.getElementById("totalPrice").textContent = `₹${total}`;
    }

    // ⚡ Listen for changes in localStorage from Page 8 without reloading
    window.addEventListener("storage", (event) => {
      if (event.key === TABLE_KEY) {
        renderCart();
      }
    });

    function checkout() {
      const cart = JSON.parse(localStorage.getItem(TABLE_KEY)) || [];
      if (cart.length === 0) return alert("Cart is empty!");

      const payload = {
        table: TABLE_NAME,
        items: cart,
        total: cart.reduce((sum, i) => sum + i.price, 0),
        time: new Date().toLocaleTimeString()
      };

      let history = JSON.parse(localStorage.getItem("master_checkout_orders")) || [];
      history.push(payload);
      localStorage.setItem("master_checkout_orders", JSON.stringify(history));

      channel.postMessage(payload);
      localStorage.removeItem(TABLE_KEY);
      
      alert("Order Checked Out Successfully!");
      window.location.href = "table8_page8.html";
    }

    renderCart();
  </script>
</body>
</html>
