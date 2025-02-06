# Smart-Hotel-Booking
Smart Hotel Booking System – A web app for browsing hotels, checking availability, and booking rooms in real-time. Built with Node.js, PostgreSQL (AWS RDS), React.js, and deployed on AWS EC2 &amp; API Gateway.
// Backend - Node.js + Express + PostgreSQL
// File: server.js

const express = require("express");
const { Pool } = require("pg");
const cors = require("cors");
const dotenv = require("dotenv");

dotenv.config();
const app = express();
app.use(express.json());
app.use(cors());

const pool = new Pool({
    user: process.env.DB_USER,
    host: process.env.DB_HOST,
    database: process.env.DB_NAME,
    password: process.env.DB_PASS,
    port: 5432,
});

// Get available hotels
app.get("/hotels", async (req, res) => {
    try {
        const result = await pool.query("SELECT * FROM hotels WHERE available_rooms > 0");
        res.json(result.rows);
    } catch (err) {
        res.status(500).json({ error: err.message });
    }
});

// Book a hotel room
app.post("/book", async (req, res) => {
    try {
        const { user_id, hotel_id, check_in_date, check_out_date } = req.body;
        await pool.query(
            "INSERT INTO bookings (user_id, hotel_id, check_in_date, check_out_date, status) VALUES ($1, $2, $3, $4, 'CONFIRMED')",
            [user_id, hotel_id, check_in_date, check_out_date]
        );
        res.json({ message: "Booking confirmed!" });
    } catch (err) {
        res.status(500).json({ error: err.message });
    }
});

app.listen(3000, () => console.log("Server running on port 3000"));
