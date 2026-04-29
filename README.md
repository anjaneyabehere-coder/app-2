import React, { useState } from 'react';

export default function BulkEmailApp() {
  const [step, setStep] = useState(1);
  const [userEmail, setUserEmail] = useState('');
  const [otp, setOtp] = useState('');
  const [isLinked, setIsLinked] = useState(false);

  const [cc, setCc] = useState('example@cc.com');
  const [mailBody, setMailBody] = useState('Enter your message here...');
  const [isEditingCc, setIsEditingCc] = useState(false);
  const [isEditingBody, setIsEditingBody] = useState(false);

  const [recipientList, setRecipientList] = useState([]);
  const [sentStatus, setSentStatus] = useState({});

  // ✅ Send OTP
  const handleSendOTP = () => {
    if (!userEmail) {
      alert("Enter email first");
      return;
    }

    alert(`OTP sent to ${userEmail}`);
    // TODO: Call backend API
  };

  // ✅ Verify OTP
  const handleVerifyOTP = () => {
    if (otp === "1234") {
      setIsLinked(true);
    } else {
      alert("Invalid OTP");
    }
  };

  // ✅ Parse emails (paste or type)
  const handlePasteEmails = (e) => {
    const emails = e.target.value
      .split(/[\n, ,]+/)
      .map(e => e.trim())
      .filter(e => e.includes('@'));

    setRecipientList(emails);
  };

  // ✅ Update recipient manually
  const updateRecipient = (index, value) => {
    const updated = [...recipientList];
    updated[index] = value;
    setRecipientList(updated);
  };

  // ✅ Send email (mock)
  const sendEmailTo = (target) => {
    console.log("Sending:", {
      from: userEmail,
      to: target,
      cc,
      mailBody
    });

    setSentStatus(prev => ({
      ...prev,
      [target]: true
    }));
  };

  return (
    <div style={{ padding: 40, maxWidth: 800, margin: 'auto', fontFamily: 'sans-serif' }}>

      {/* STEP 1: LINK EMAIL */}
      {step === 1 && (
        <div style={{ border: '1px solid #ddd', padding: 20, borderRadius: 8 }}>
          <h3>Link Your Account</h3>

          <input
            type="email"
            placeholder="Enter your email"
            value={userEmail}
            onChange={(e) => setUserEmail(e.target.value)}
            style={{ padding: 8 }}
          />

          <button onClick={handleSendOTP} style={{ marginLeft: 10 }}>
            Send OTP
          </button>

          <div style={{ marginTop: 20 }}>
            <input
              placeholder="Enter OTP"
              value={otp}
              onChange={(e) => setOtp(e.target.value)}
              style={{ padding: 8 }}
            />
            <button onClick={handleVerifyOTP} style={{ marginLeft: 10 }}>
              Verify
            </button>
          </div>

          {isLinked && (
            <>
              <p style={{ color: 'green', marginTop: 10 }}>
                Email linked successfully
              </p>
              <button onClick={() => setStep(2)}>Next</button>
            </>
          )}
        </div>
      )}

      {/* STEP 2: COMPOSE */}
      {step === 2 && (
        <div style={{ border: '1px solid #ddd', padding: 20, borderRadius: 8 }}>
          <h3>Compose Email</h3>

          {/* CC */}
          <div onDoubleClick={() => setIsEditingCc(true)} style={{ marginBottom: 10 }}>
            <strong>CC: </strong>
            {isEditingCc ? (
              <input
                value={cc}
                onChange={(e) => setCc(e.target.value)}
                onBlur={() => setIsEditingCc(false)}
                autoFocus
              />
            ) : (
              <span>{cc} (double-click to edit)</span>
            )}
          </div>

          {/* BODY */}
          <div
            onDoubleClick={() => setIsEditingBody(true)}
            style={{
              border: '1px solid #eee',
              padding: 15,
              minHeight: 100,
              background: '#f9f9f9'
            }}
          >
            {isEditingBody ? (
              <textarea
                value={mailBody}
                onChange={(e) => setMailBody(e.target.value)}
                onBlur={() => setIsEditingBody(false)}
                style={{ width: '100%', height: 100 }}
                autoFocus
              />
            ) : (
              <p>{mailBody}</p>
            )}
          </div>

          {/* RECIPIENT INPUT */}
          <div style={{ marginTop: 30 }}>
            <h4>Paste Emails</h4>
            <textarea
              placeholder="Paste emails separated by comma, space, or new line"
              onChange={handlePasteEmails}
              style={{ width: '100%', height: 80 }}
            />
          </div>

          {/* LIST */}
          <div style={{ marginTop: 20 }}>
            {recipientList.map((rec, index) => (
              <div key={index} style={{ display: 'flex', gap: 10, marginBottom: 10 }}>
                <input
                  value={rec}
                  onChange={(e) => updateRecipient(index, e.target.value)}
                  style={{ width: 250 }}
                />

                <button onClick={() => sendEmailTo(rec)}>
                  Send
                </button>

                {sentStatus[rec] && (
                  <span style={{ color: 'green', fontSize: 12 }}>
                    Sent ✓
                  </span>
                )}
              </div>
            ))}
          </div>
        </div>
      )}
    </div>
  );
}
