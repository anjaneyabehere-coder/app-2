import React, { useState } from 'react';

const EmailApp = () => {
  const [step, setStep] = useState(1); // 1: Link, 2: Compose
  const [email, setEmail] = useState('');
  const [otp, setOtp] = useState('');
  const [linked, setLinked] = useState(false);
  
  // Email Content States
  const [cc, setCc] = useState('example@cc.com');
  const [mailBody, setMailBody] = useState('Write your message here...');
  const [recipients, setRecipients] = useState([]);
  const [isEditingBody, setIsEditingBody] = useState(false);

  // 1. Linking Logic
  const handleSendOTP = () => {
    alert(`OTP sent to ${email}`);
    // Backend call would happen here
  };

  const verifyOTP = () => {
    if (otp === "1234") { // Simplified for example
      setLinked(true);
    }
  };

  // 2. Recipient Logic
  const handlePasteRecipients = (e) => {
    const pastedData = e.target.value.split('\n');
    setRecipients(pastedData);
  };

  const sendMailToUser = (targetEmail) => {
    alert(`Email sent successfully to ${targetEmail}`);
    // This would trigger your backend SMTP function
  };

  return (
    <div style={{ padding: '20px', maxWidth: '600px', margin: 'auto' }}>
      {step === 1 ? (
        <section>
          <h2>Link Your Email</h2>
          <input 
            placeholder="Enter your email ID" 
            onChange={(e) => setEmail(e.target.value)} 
          />
          <button onClick={handleSendOTP}>Send a OTP to link your email</button>
          
          <div style={{ marginTop: '10px' }}>
            <input placeholder="Enter OTP" onChange={(e) => setOtp(e.target.value)} />
            <button onClick={verifyOTP}>Verify</button>
          </div>

          {linked && <p style={{ color: 'green' }}>Email linked successfully</p>}
          
          {linked && (
            <button 
              style={{ position: 'fixed', bottom: '20px', right: '20px' }}
              onClick={() => setStep(2)}
            >
              Next
            </button>
          )}
        </section>
      ) : (
        <section>
          <h2>Compose Email</h2>
          {/* Double click to edit CC */}
          <div onDoubleClick={() => console.log("Editing CC")}>
            <strong>CC:</strong> 
            <input value={cc} onChange={(e) => setCc(e.target.value)} />
          </div>

          {/* Double click to edit Body */}
          <div onDoubleClick={() => setIsEditingBody(true)} style={{ border: '1px solid #ccc', minHeight: '100px', padding: '10px' }}>
            {isEditingBody ? (
              <textarea 
                fullWidth 
                onBlur={() => setIsEditingBody(false)}
                value={mailBody} 
                onChange={(e) => setMailBody(e.target.value)} 
              />
            ) : (
              <p>{mailBody}</p>
            )}
          </div>

          <h3>Paste Recipient Email IDs</h3>
          <textarea 
            placeholder="Double click to paste list"
            onDoubleClick={() => {/* Trigger paste prompt */}}
            onChange={handlePasteRecipients}
            style={{ width: '100%', height: '100px' }}
          />

          <div style={{ marginTop: '20px' }}>
            {recipients.map((rec, index) => (
              <div key={index} style={{ display: 'flex', justifyContent: 'space-between', marginBottom: '5px' }}>
                <input defaultValue={rec} />
                <button onClick={() => sendMailToUser(rec)}>Send</button>
              </div>
            ))}
          </div>
        </section>
      )}
    </div>
  );
};

export default EmailApp;
