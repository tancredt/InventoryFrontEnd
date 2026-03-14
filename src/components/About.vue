<template>
  <div class="about-page">
    <div class="about-container">
      <h1>About the FRV Hazmat Equipment Inventory System</h1>
      
      <section class="about-section">
        <h2>System Purpose</h2>
        <p>
          The Fire Rescue Victoria Hazmat Equipment Inventory System is a comprehensive internal 
          application designed to track, manage, and maintain all hazardous materials detection 
          equipment used by FRV personnel. This system ensures that critical safety equipment 
          is properly maintained, calibrated, and available for emergency response operations 
          across all FRV stations and facilities.
        </p>
      </section>

      <section class="about-section">
        <h2>Equipment Categories</h2>
        <div class="equipment-types">
          <div class="equipment-type-card">
            <h3>Gas Detectors</h3>
            <p>
              Portable and fixed gas detection instruments including:
            </p>
            <ul>
              <li><strong>MicroRAE</strong> - Compact personal gas monitor</li>
              <li><strong>MultiRAE</strong> - Multi-gas detector with PID sensor</li>
              <li><strong>ToxiRAE</strong> - Single-gas personal monitor</li>
              <li><strong>AreaRAE</strong> - Wireless area monitoring system</li>
            </ul>
          </div>
          
          <div class="equipment-type-card">
            <h3>Calibration Equipment</h3>
            <p>
              Equipment used for testing and calibrating detectors:
            </p>
            <ul>
              <li><strong>Calibration Cylinders</strong> - Known concentration gas standards</li>
              <li><strong>Regulators</strong> - Flow control devices</li>
              <li><strong>Calibration Caps</strong> - Adapter fittings for detectors</li>
              <li><strong>Bump Test Stations</strong> - Quick functional test equipment</li>
            </ul>
          </div>
          
          <div class="equipment-type-card">
            <h3>Sensors</h3>
            <p>
              Replaceable sensor modules for gas detectors:
            </p>
            <ul>
              <li><strong>PID Sensors</strong> - Photoionization detectors for VOCs</li>
              <li><strong>Electrochemical Sensors</strong> - For toxic gases (CO, H2S, etc.)</li>
              <li><strong>Catalytic Bead Sensors</strong> - For combustible gases (LEL)</li>
              <li><strong>Infrared Sensors</strong> - For CO2 and hydrocarbons</li>
            </ul>
          </div>
        </div>
      </section>

      <section class="about-section">
        <h2>System Features</h2>
        <div class="features-grid">
          <div class="feature-card">
            <h3>Inventory Tracking</h3>
            <p>Real-time tracking of all equipment locations across FRV stations, including current status and assignment history.</p>
          </div>
          <div class="feature-card">
            <h3>Maintenance Scheduling</h3>
            <p>Automated scheduling for regular calibration, bump tests, and scheduled services to ensure equipment reliability.</p>
          </div>
          <div class="feature-card">
            <h3>Fault Management</h3>
            <p>Comprehensive fault reporting and tracking system for equipment issues, from initial report to resolution.</p>
          </div>
          <div class="feature-card">
            <h3>Sensor Management</h3>
            <p>Track sensor installation dates, warranty periods, and expiry dates to ensure timely replacements.</p>
          </div>
          <div class="feature-card">
            <h3>Cylinder Tracking</h3>
            <p>Monitor calibration gas cylinder contents, expiry dates, and locations to maintain calibration capability.</p>
          </div>
          <div class="feature-card">
            <h3>Reporting</h3>
            <p>Generate PDF reports for audits, maintenance records, and equipment status summaries.</p>
          </div>
        </div>
      </section>

      <section class="about-section">
        <h2>Compliance and Standards</h2>
        <p>
          This system helps FRV maintain compliance with relevant Australian Standards and regulations 
          for hazardous materials detection equipment, including:
        </p>
        <ul>
          <li>AS/NZS 60079.29.2 - Explosive atmospheres - Gas detectors</li>
          <li>AS/NZS 1678 - Emergency procedures for workplace incidents</li>
          <li>Manufacturer recommended calibration and maintenance intervals</li>
          <li>FRV Operational Procedures for Hazmat response</li>
        </ul>
      </section>

      <section class="about-section">
        <h2>System Administration</h2>
        <p>
          The FRV Hazmat Equipment Inventory System is maintained by the FRV Hazmat Department. 
          For system support, equipment questions, or training requests, please contact:
        </p>
        <div class="contact-info">
          <p><strong>Email:</strong> hazmat.support@frv.vic.gov.au</p>
          <p><strong>Phone:</strong> (03) XXXX XXXX</p>
          <p><strong>Hours:</strong> Monday to Friday, 8:00 AM - 5:00 PM</p>
        </div>
      </section>
    </div>
  </div>
</template>

<script setup>
</script>

<style scoped>
.about-page {
  min-height: 100vh;
  background: #f5f5f5;
  padding: 2rem;
}

.about-container {
  max-width: 1200px;
  margin: 0 auto;
}

.about-container h1 {
  color: #2c3e50;
  font-size: 2rem;
  margin-bottom: 2rem;
  text-align: center;
}

.about-section {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  margin-bottom: 2rem;
}

.about-section h2 {
  color: #667eea;
  font-size: 1.5rem;
  margin-bottom: 1rem;
  border-bottom: 2px solid #667eea;
  padding-bottom: 0.5rem;
}

.about-section h3 {
  color: #2c3e50;
  font-size: 1.2rem;
  margin-bottom: 0.75rem;
}

.about-section p {
  color: #666;
  line-height: 1.6;
  margin-bottom: 1rem;
}

.about-section ul {
  color: #666;
  line-height: 1.8;
  padding-left: 1.5rem;
}

.about-section li {
  margin-bottom: 0.5rem;
}

.equipment-types {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1.5rem;
  margin-top: 1rem;
}

.equipment-type-card {
  background: #f8f9fa;
  padding: 1.5rem;
  border-radius: 6px;
  border-left: 4px solid #667eea;
}

.features-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.5rem;
  margin-top: 1rem;
}

.feature-card {
  background: #f8f9fa;
  padding: 1.5rem;
  border-radius: 6px;
}

.feature-card h3 {
  color: #667eea;
  margin-bottom: 0.5rem;
}

.contact-info {
  background: #f8f9fa;
  padding: 1.5rem;
  border-radius: 6px;
  margin-top: 1rem;
}

.contact-info p {
  margin: 0.5rem 0;
}

@media (max-width: 768px) {
  .about-page {
    padding: 1rem;
  }

  .about-container h1 {
    font-size: 1.5rem;
  }

  .about-section {
    padding: 1.5rem;
  }

  .equipment-types,
  .features-grid {
    grid-template-columns: 1fr;
  }
}
</style>
