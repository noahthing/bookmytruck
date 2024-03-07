# bookmytruck
npm install express mongoose body-parser
// Import necessary modules
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');

// Create an instance of Express app
const app = express();

// Connect to MongoDB
mongoose.connect('mongodb://localhost/jobboard', { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('Connected to MongoDB'))
  .catch(err => console.error('Could not connect to MongoDB', err));

// Define Job schema
const jobSchema = new mongoose.Schema({
  title: String,
  description: String,
  company: String,
  location: String,
  salary: Number,
  datePosted: { type: Date, default: Date.now }
});

// Define Job model
const Job = mongoose.model('Job', jobSchema);

// Middleware to parse JSON
app.use(bodyParser.json());

// Endpoint to post a new job
app.post('/jobs', async (req, res) => {
  try {
    const jobData = req.body;
    const job = new Job(jobData);
    await job.save();
    res.status(201).json(job);
  } catch (error) {
    res.status(400).json({ message: error.message });
  }
});

// Endpoint to get all jobs
app.get('/jobs', async (req, res) => {
  try {
    const jobs = await Job.find().sort({ datePosted: -1 });
    res.json(jobs);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
});

// Set up the server to listen on port 3000
const port = process.env.PORT || 3000;
app.listen(port, () => console.log(`Server is running on port ${port}`));
