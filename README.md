import React, { useState, useEffect } from 'react';
import './StudentData.css'; // You'll need a basic CSS file for styling

// --- 1. Dummy API Simulation ---
// In a real application, replace this function with your actual 'fetch' or 'axios' call
const simulateFetchStudents = () => {
  return new Promise((resolve, reject) => {
    // Simulate network delay
    setTimeout(() => {
      // 10% chance of failure to test the error state
      if (Math.random() < 0.1) {
        reject(new Error('Network Error: Failed to fetch student data.'));
        return;
      }

      // Successful data payload
      const studentData = [
        { id: 101, name: 'Alice Johnson', major: 'Computer Science', gpa: 3.9 },
        { id: 102, name: 'Bob Smith', major: 'Electrical Engineering', gpa: 3.5 },
        { id: 103, name: 'Charlie Brown', major: 'Business Administration', gpa: 3.2 },
        { id: 104, name: 'Diana Prince', major: 'Physics', gpa: 4.0 },
        { id: 105, name: 'Ethan Hunt', major: 'Mathematics', gpa: 3.7 },
      ];
      resolve(studentData);
    }, 1500); // 1.5 second delay
  });
};

const StudentDataFetcher = () => {
  const [students, setStudents] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // --- 2. useEffect for Data Fetching ---
  useEffect(() => {
    // A flag to prevent setting state on an unmounted component (good practice)
    let isMounted = true; 

    const fetchStudents = async () => {
      setLoading(true);
      setError(null);
      
      try {
        // --- Using the simulated API ---
        const data = await simulateFetchStudents();

        // --- If using real 'fetch' ---
        /*
        // const response = await fetch('YOUR_API_ENDPOINT_HERE');
        // if (!response.ok) {
        //   throw new Error(`HTTP error! Status: ${response.status}`);
        // }
        // const data = await response.json();
        */

        if (isMounted) {
          setStudents(data);
        }
      } catch (err) {
        if (isMounted) {
          console.error("Fetch Error:", err);
          setError(err.message || 'An unknown error occurred during fetch.');
        }
      } finally {
        if (isMounted) {
          setLoading(false);
        }
      }
    };

    fetchStudents();

    // Cleanup function: runs when the component unmounts
    return () => {
      isMounted = false;
    };
  }, []); // Empty dependency array ensures it runs only once on mount

  // --- 3. Render Logic: Handle States Gracefully ---

  if (loading) {
    return (
      <div className="container">
        <p className="loading-state">
          <span className="spinner">🔄</span> **Loading student data...** Please wait.
        </p>
      </div>
    );
  }

  if (error) {
    return (
      <div className="container">
        <div className="error-state">
          <span className="error-icon">❌</span> **Error:** {error}
          <p>Could not retrieve data. The server may be unavailable.</p>
        </div>
      </div>
    );
  }

  if (students.length === 0) {
    return (
      <div className="container">
        <p className="no-data-state">
          <span className="info-icon">ℹ️</span> **No student records found.**
        </p>
      </div>
    );
  }

  // --- 4. Display Data in a Table (or Cards) ---
  return (
    <div className="container">
      <h2>🎓 Student Records (Table View)</h2>
      <table className="student-table">
        <thead>
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Major</th>
            <th>GPA</th>
          </tr>
        </thead>
        <tbody>
          {students.map((student) => (
            <tr key={student.id}>
              <td>{student.id}</td>
              <td>**{student.name}**</td>
              <td>{student.major}</td>
              <td>{student.gpa.toFixed(2)}</td>
            </tr>
          ))}
        </tbody>
      </table>
      
      {/* --- Alternative: Cards View --- */}
      <h3>Student Cards View</h3>
      <div className="card-container">
        {students.map((student) => (
          <div key={student.id} className="student-card">
            <h4>{student.name} (ID: {student.id})</h4>
            <p><strong>Major:</strong> {student.major}</p>
            <p><strong>GPA:</strong> {student.gpa.toFixed(2)}</p>
          </div>
        ))}
      </div>
    </div>
  );
};

export default StudentDataFetcher;
