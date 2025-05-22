# Hospital-management-
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Hospital Management System</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color:lightgrey;
      color:#333;
      margin: 0;
      padding: 0;
    }
    header {
      background-color: #2c3e50;
      color: rgb(255, 255, 255);
      text-align: center;
      padding: 1rem;
    }
    nav {
      text-align: center;
      margin-top: 10px;
    }
    nav a {
      color: lightgray;
      margin: 0 15px;
      text-decoration: none;
      font-weight: bold;
      font-size: 1.2rem;
    }
    nav a:hover {
        color: #1abc9c;
    }
    main {
      padding: 20px;
    }
    .section {
      display: none;
    }
    .section.visible {
      display: block;
    }
    .card-container {
      display: flex;
      justify-content: space-around;
      margin-top: 20px;
    }
    .card {
      background-color: #f8f8f8;
      padding: 20px;
      border: 2px solid #236e81;
      border-radius: 10px;
      text-align: center;
      width: 22%;
      cursor: pointer;
      transition: transform 0.3s;
    }
    .card:hover {
      transform: scale(1.05);
    }
    .registry ul {
      list-style: none;
      padding: 0;
    }
    .registry li {
      padding: 10px;
      background-color: #8f1818;
      margin: 5px 0;
      border-radius: 5px;
      border: 1px solid #ddd;
    }
    button {
      padding: 10px;
      background-color: #ff00b3;
      color: lightgray;
      border: none;
      cursor: pointer;
      font-weight: bold;
      border-radius: 5px;
      margin-top: 10px;
    }
    button:hover {
      background-color: #4cabb8;
    }
    input, select {
      padding: 10px;
      margin: 5px 0;
      width: 100%;
      border: 1px solid #ddd;
      border-radius: 5px;
    }
  </style>
</head>
<body>

  <header>
    <h1>🏥 Hospital Management System</h1>
    <nav>
      <a onclick="showSection('home')">Home</a>
      <a onclick="showSection('doctors')">Doctors</a>
      <a onclick="showSection('patients')">Patients</a>
      <a onclick="showSection('appointments')">Appointments</a>
      <a onclick="showSection('patientList')">Patient List</a> <!-- NEW -->
    </nav>
  </header>

  <main>
    <!-- HOME SECTION -->
    <section id="home" class="section visible">
      <div class="card-container">
        <div class="card" onclick="showSection('doctors')">
          <h2>Doctors</h2>
          <p>Manage doctor profiles, departments, and schedules.</p>
        </div>
        <div class="card" onclick="showSection('patients')">
          <h2>Patients</h2>
          <p>Track patient records, history, and treatment plans.</p>
        </div>
        <div class="card" onclick="showSection('appointments')">
          <h2>Appointments</h2>
          <p>Schedule and review upcoming appointments with ease.</p>
        </div>
        <div class="card" onclick="showSection('patientList')">
          <h2>Patient List</h2>
          <p>View all registered patients and their registration dates.</p>
        </div>
      </div>
    </section>

    <!-- DOCTOR SECTION -->
    <section id="doctors" class="section">
      <h2 style="text-align: center;">Doctor Availability</h2>
      <form>
        <label for="doctor">Select Doctor:</label>
        <select id="doctor" onchange="showAvailability()">
          <option value="">-- Choose Doctor --</option>
          <option value="dr_smith">Dr. Smith (Cardiology)</option>
          <option value="dr_jones">Dr. Jones (Pediatrics)</option>
          <option value="dr_brown">Dr. Brown (Orthopedics)</option>
        </select>
        <div id="availability"></div>
      </form>
    </section>

    <!-- PATIENT REGISTRATION SECTION -->
    <section id="patients" class="section">
      <h2 style="text-align: center;">Patient Registration</h2>
      <form id="patientForm" onsubmit="handlePatientSubmit(event)">
        <input type="text" id="pname" placeholder="Full Name" required />
        <input type="number" id="age" placeholder="Age" required />
        <input type="text" id="disease" placeholder="Disease / Concern" required />
        <input type="tel" id="phone" placeholder="Mobile Number" pattern="[0-9]{10}" required />
        <input type="email" id="email" placeholder="Email Address" required />
        <button type="submit">Register Patient</button>
        <div id="patientConfirmation" style="color: green; margin-top: 10px;"></div>
      </form>
    </section>

    <!-- ✅ NEW PATIENT LIST SECTION -->
    <section id="patientList" class="section">
      <h2 style="text-align: center;">Registered Patients</h2>
      <div class="registry">
        <ul id="patientListContent"></ul>
        <button onclick="downloadCSV()" style="margin-top: 15px; padding: 10px; font-weight: bold;">⬇️ Download as CSV</button>
      </div>
    </section>

    <!-- APPOINTMENT SECTION -->
    <section id="appointments" class="section">
      <h2 style="text-align: center;">Schedule Appointment</h2>
      <form>
        <input type="text" placeholder="Patient Name" required />
        <input type="date" required />
        <select>
          <option value="">-- Select Doctor --</option>
          <option value="dr_smith">Dr. Smith</option>
          <option value="dr_jones">Dr. Jones</option>
          <option value="dr_brown">Dr. Brown</option>
        </select>
        <button type="submit">Book Appointment</button>
      </form>
    </section>
  </main>

  <footer>
    <p>© 2025 Hospital Management System</p>
  </footer>

  <script>
    function showSection(id) {
      const sections = document.querySelectorAll('.section');
      sections.forEach(sec => sec.classList.remove('visible'));
      document.getElementById(id).classList.add('visible');
      if (id === "patientList") {
        loadPatientsToList();
      }
    }

    function showAvailability() {
      const doctor = document.getElementById("doctor").value;
      const availability = {
        dr_smith: "Monday to Friday, 9:00 AM – 1:00 PM",
        dr_jones: "Monday, Wednesday, Friday, 2:00 PM – 6:00 PM",
        dr_brown: "Tuesday and Thursday, 10:00 AM – 4:00 PM"
      };
      document.getElementById("availability").textContent = availability[doctor] || "";
    }

    function handlePatientSubmit(event) {
      event.preventDefault();

      const name = document.getElementById("pname").value;
      const age = document.getElementById("age").value;
      const disease = document.getElementById("disease").value;
      const phone = document.getElementById("phone").value;
      const email = document.getElementById("email").value;
      const date = new Date().toLocaleDateString();

      const patient = { name, age, disease, phone, email, date };

      // Save to localStorage
      let patients = JSON.parse(localStorage.getItem("patients")) || [];
      patients.push(patient);
      localStorage.setItem("patients", JSON.stringify(patients));

      // Show confirmation
      document.getElementById("patientConfirmation").textContent = "✅ Patient Registered Successfully!";
      document.getElementById("patientForm").reset();
    }

    function loadPatientsToList() {
      const list = document.getElementById("patientListContent");
      const patients = JSON.parse(localStorage.getItem("patients")) || [];
      list.innerHTML = "";

      if (patients.length === 0) {
        list.innerHTML = "<li>No patients registered yet.</li>";
        return;
      }

      patients.forEach((p, i) => {
        const item = document.createElement("li");
        item.textContent = `${i + 1}. ${p.name} | Age: ${p.age} | Phone: ${p.phone} | Email: ${p.email} | Date: ${p.date}`;
        list.appendChild(item);
      });
    }

    function downloadCSV() {
      const patients = JSON.parse(localStorage.getItem("patients")) || [];

      if (patients.length === 0) {
        alert("No patient data to download.");
        return;
      }

      const headers = ["Name", "Age", "Disease", "Phone", "Email", "Date"];
      const rows = patients.map(p => [p.name, p.age, p.disease, p.phone, p.email, p.date]);

      let csvContent = "data:text/csv;charset=utf-8," 
        + headers.join(",") + "\n" 
        + rows.map(row => row.join(",")).join("\n");

      const encodedUri = encodeURI(csvContent);
      const link = document.createElement("a");
      link.setAttribute("href", encodedUri);
      link.setAttribute("download", "patients_list.csv");
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }
  </script>

</body>
</html>
