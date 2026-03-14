<template>
  <div class="help-page">
    <div class="help-container">
      <h1>Help & Frequently Asked Questions</h1>
      
      <section class="help-section">
        <h2>Getting Started</h2>
        <div class="faq-item">
          <h3>How do I log in to the system?</h3>
          <p>
            Use your FRV network credentials to log in. If you don't have an account or are 
            experiencing login issues, please contact the FRV Hazmat Department for assistance.
          </p>
        </div>
        
        <div class="faq-item">
          <h3>What can I do in this system?</h3>
          <p>
            The system allows you to view and manage hazmat equipment including gas detectors, 
            calibration cylinders, and sensors. You can track equipment locations, schedule 
            maintenance, report faults, and generate reports.
          </p>
        </div>
      </section>

      <section class="help-section">
        <h2>Detectors</h2>
        <div class="faq-item">
          <h3>How do I add a new detector?</h3>
          <p>
            Navigate to the Detectors page and click "Add New Detector". Fill in the required 
            information including label, serial number, detector model, and initial location. 
            The system will automatically create sensor slots based on the detector model configuration.
          </p>
        </div>
        
        <div class="faq-item">
          <h3>How do I change a detector's location?</h3>
          <p>
            Use the LocationApp application for changing detector locations. This ensures proper 
            tracking of equipment movements between stations and the Burnley facility.
          </p>
        </div>
        
        <div class="faq-item">
          <h3>What detector models are supported?</h3>
          <p>
            The system supports various detector models including:
          </p>
          <ul>
            <li>Honeywell/Rae - MicroRAE, MultiRAE, ToxiRAE, AreaRAE</li>
            <li>MSA - Altair series multigas detectors</li>
            <li>Draeger - X-am series detectors</li>
            <li>Proengin - PID detectors</li>
          </ul>
        </div>
        
        <div class="faq-item">
          <h3>What do the detector status codes mean?</h3>
          <p>
            <strong>On Order (OO)</strong> - Equipment has been ordered but not yet received<br>
            <strong>Offline Repair (OF)</strong> - Equipment is away for repair<br>
            <strong>In Stock (IS)</strong> - Equipment is available in inventory<br>
            <strong>Operational (OP)</strong> - Equipment is deployed and operational<br>
            <strong>Decommissioned (DC)</strong> - Equipment has been retired from service
          </p>
        </div>
      </section>

      <section class="help-section">
        <h2>Calibration Cylinders</h2>
        <div class="faq-item">
          <h3>How do I add new calibration cylinders?</h3>
          <p>
            Navigate to the Cylinders page and click "Add New Cylinder". You'll need to select 
            a cylinder type (which defines the gas mixture) and assign an initial location.
          </p>
        </div>
        
        <div class="faq-item">
          <h3>How do I know when a cylinder is expired?</h3>
          <p>
            The system tracks expiry dates for all calibration cylinders. Expired cylinders 
            will be highlighted in the cylinder list. Cylinder types have predefined expiry 
            periods (typically 12-24 months from fill date).
          </p>
        </div>
        
        <div class="faq-item">
          <h3>What gases are available for calibration?</h3>
          <p>
            Common calibration gases include:
          </p>
          <ul>
            <li>Carbon Monoxide (CO)</li>
            <li>Hydrogen Sulfide (H2S)</li>
            <li>Methane (CH4) / LEL</li>
            <li>Oxygen (O2)</li>
            <li>Isobutylene (for PID calibration)</li>
            <li>Hydrogen Cyanide (HCN)</li>
            <li>Sulfur Dioxide (SO2)</li>
            <li>Nitrogen Dioxide (NO2)</li>
          </ul>
        </div>
      </section>

      <section class="help-section">
        <h2>Sensors</h2>
        <div class="faq-item">
          <h3>How do I replace a sensor?</h3>
          <p>
            Navigate to the detector details page, find the sensor slot you want to replace, 
            and click "Edit". You can remove the old sensor and install a new one. The system 
            will track installation dates and calculate warranty and expiry dates automatically.
          </p>
        </div>
        
        <div class="faq-item">
          <h3>What is the difference between warranty and expiry dates?</h3>
          <p>
            <strong>Warranty Date</strong> - The date until which the sensor is covered by 
            manufacturer warranty (typically 12-24 months from purchase)<br>
            <strong>Expiry Date</strong> - The date when the sensor should be replaced 
            regardless of use (sensor shelf life, typically 24-36 months from manufacture)
          </p>
        </div>
        
        <div class="faq-item">
          <h3>What sensor types are available?</h3>
          <p>
            <strong>PID (Photoionization Detector)</strong> - For volatile organic compounds (VOCs)<br>
            <strong>Electrochemical</strong> - For toxic gases like CO, H2S, Cl2, NH3<br>
            <strong>Catalytic Bead</strong> - For combustible gases (LEL)<br>
            <strong>Infrared (IR)</strong> - For CO2 and hydrocarbon detection
          </p>
        </div>
      </section>

      <section class="help-section">
        <h2>Maintenance</h2>
        <div class="faq-item">
          <h3>What types of maintenance are tracked?</h3>
          <p>
            <strong>Scheduled Service (SV)</strong> - Regular 6-monthly or annual service<br>
            <strong>Unscheduled Service (MS)</strong> - Service due to fault or damage<br>
            <strong>Battery Replacement (BT)</strong> - Battery change record<br>
            <strong>Filter Replacement (FC)</strong> - Filter change record<br>
            <strong>Dessicant Replacement (DR)</strong> - Moisture trap replacement<br>
            <strong>Clean (CL)</strong> - General cleaning and inspection
          </p>
        </div>
        
        <div class="faq-item">
          <h3>What maintenance tasks can be recorded?</h3>
          <p>
            <strong>Calibration (CB)</strong> - Full calibration with certified gas<br>
            <strong>Bump Test (BM)</strong> - Functional test with gas<br>
            <strong>Sensor Replacement (SN)</strong> - Installing new sensor<br>
            <strong>Display Replacement (DS)</strong> - Screen replacement<br>
            <strong>Battery Replacement (BT)</strong> - Power source replacement<br>
            <strong>Board Replacement (BD)</strong> - Circuit board replacement<br>
            <strong>Repair (RP)</strong> - General repair work
          </p>
        </div>
        
        <div class="faq-item">
          <h3>How often should detectors be calibrated?</h3>
          <p>
            Calibration frequency depends on the detector model and usage. Generally:
          </p>
          <ul>
            <li>MultiRAE/MicroRAE - Every 6 months or after sensor replacement</li>
            <li>ToxiRAE - Every 6 months</li>
            <li>AreaRAE - Every 6 months</li>
            <li>After any unscheduled service or repair</li>
          </ul>
        </div>
      </section>

      <section class="help-section">
        <h2>Fault Reporting</h2>
        <div class="faq-item">
          <h3>How do I report a fault?</h3>
          <p>
            Navigate to the detector details page and click "Report Fault". Select the fault 
            type, enter the report date and location, and add any relevant notes. The system 
            will create a fault record that can be tracked until resolution.
          </p>
        </div>
        
        <div class="faq-item">
          <h3>What fault types can be reported?</h3>
          <p>
            <strong>Failed Bump (BF)</strong> - Detector failed bump test<br>
            <strong>Sensor Fail (SF)</strong> - Sensor not responding correctly<br>
            <strong>Calibration Expired (CE)</strong> - Calibration overdue<br>
            <strong>Displays Error (DE)</strong> - Display showing errors<br>
            <strong>Will not turn on (WS)</strong> - Power failure<br>
            <strong>Damaged Display (DD)</strong> - Physical display damage<br>
            <strong>Damaged Casing (DC)</strong> - Physical case damage<br>
            <strong>Missing Attachment (MA)</strong> - Missing clip, boot, or filter
          </p>
        </div>
      </section>

      <section class="help-section">
        <h2>Reports</h2>
        <div class="faq-item">
          <h3>What reports are available?</h3>
          <p>
            The system can generate PDF reports for:
          </p>
          <ul>
            <li>All detectors with filter options</li>
            <li>All sensors with expiry date filtering</li>
            <li>All calibration cylinders</li>
            <li>Maintenance due/scheduled</li>
            <li>Open faults</li>
            <li>Individual detector history</li>
          </ul>
        </div>
        
        <div class="faq-item">
          <h3>How do I generate a report?</h3>
          <p>
            Each main page (Detectors, Sensors, Cylinders, Maintenance, Faults) has a 
            "Generate PDF Report" button. You can apply filters before generating to 
            customize the report content.
          </p>
        </div>
      </section>

      <section class="help-section">
        <h2>Technical Support</h2>
        <div class="faq-item">
          <h3>Who do I contact for help?</h3>
          <p>
            For system support, equipment questions, or training requests:
          </p>
          <ul>
            <li><strong>Email:</strong> hazmat.support@frv.vic.gov.au</li>
            <li><strong>Phone:</strong> (03) XXXX XXXX</li>
            <li><strong>Hours:</strong> Monday to Friday, 8:00 AM - 5:00 PM</li>
          </ul>
        </div>
        
        <div class="faq-item">
          <h3>What browsers are supported?</h3>
          <p>
            The system works best with modern browsers:
          </p>
          <ul>
            <li>Google Chrome (recommended)</li>
            <li>Mozilla Firefox</li>
            <li>Microsoft Edge</li>
            <li>Safari</li>
          </ul>
        </div>
      </section>
    </div>
  </div>
</template>

<script setup>
</script>

<style scoped>
.help-page {
  min-height: 100vh;
  background: #f5f5f5;
  padding: 2rem;
}

.help-container {
  max-width: 1000px;
  margin: 0 auto;
}

.help-container h1 {
  color: #2c3e50;
  font-size: 2rem;
  margin-bottom: 2rem;
  text-align: center;
}

.help-section {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  margin-bottom: 2rem;
}

.help-section h2 {
  color: #667eea;
  font-size: 1.5rem;
  margin-bottom: 1.5rem;
  border-bottom: 2px solid #667eea;
  padding-bottom: 0.5rem;
}

.faq-item {
  margin-bottom: 2rem;
  padding-bottom: 1.5rem;
  border-bottom: 1px solid #eee;
}

.faq-item:last-child {
  border-bottom: none;
  margin-bottom: 0;
  padding-bottom: 0;
}

.faq-item h3 {
  color: #2c3e50;
  font-size: 1.1rem;
  margin-bottom: 0.75rem;
}

.faq-item p {
  color: #666;
  line-height: 1.6;
  margin-bottom: 0.5rem;
}

.faq-item ul {
  color: #666;
  line-height: 1.8;
  padding-left: 1.5rem;
  margin-top: 0.5rem;
}

.faq-item li {
  margin-bottom: 0.25rem;
}

@media (max-width: 768px) {
  .help-page {
    padding: 1rem;
  }

  .help-container h1 {
    font-size: 1.5rem;
  }

  .help-section {
    padding: 1.5rem;
  }
}
</style>
