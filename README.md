document.getElementById("nutrition-form").addEventListener("submit", async (e) => {
  e.preventDefault();
  const foodItem = document.getElementById("food-item").value;
  const quantity = document.getElementById("quantity").value;

  const response = await fetch("/api/calculate-nutrients", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ foodItem, quantity }),
  });

  const result = await response.json();
  document.getElementById("result").innerHTML = `
    <p>Calories: ${result.calories}</p>
    <p>Protein: ${result.protein}g</p>
    <p>Carbs: ${result.carbs}g</p>
    <p>Fats: ${result.fats}g</p>
  `;
});

const express = require("express");
const bodyParser = require("body-parser");
const app = express();
app.use(bodyParser.json());

// Simulated food database
const foodData = {
  apple: { calories: 52, protein: 0.3, carbs: 14, fats: 0.2 },
  banana: { calories: 96, protein: 1.3, carbs: 27, fats: 0.3 },
};

app.post("/api/calculate-nutrients", (req, res) => {
  const { foodItem, quantity } = req.body;
  const food = foodData[foodItem.toLowerCase()];

  if (!food) {
    return res.status(404).json({ error: "Food not found!" });
  }

  const multiplier = quantity / 100; // Assuming data is per 100g
  res.json({
    calories: (food.calories * multiplier).toFixed(2),
    protein: (food.protein * multiplier).toFixed(2),
    carbs: (food.carbs * multiplier).toFixed(2),
    fats: (food.fats * multiplier).toFixed(2),
  });
});

app.listen(3000, () => {
  console.log("Server is running on http://localhost:3000");
});
