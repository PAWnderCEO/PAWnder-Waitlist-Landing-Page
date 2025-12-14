# PAWnder-Waitlist-Landing-Page
index.html
README.md
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Pawnder — Join your city's pack</title>
  <meta name="description" content="Pawnder — Tinder + Maps for dog lovers. Join the waitlist for Bay Area early access."/>
  <style>
    :root{--bg:#0f1724;--card:#0b1220;--accent:#f6a623;--muted:#9aa6b2;--white:#fff}
    body{margin:0;font-family:Inter,system-ui,Segoe UI,Arial;color:var(--white);background:linear-gradient(180deg,#071025 0%, #072034 100%);min-height:100vh;display:flex;align-items:center;justify-content:center;padding:24px}
    .card{width:100%;max-width:860px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border-radius:14px;padding:28px;box-shadow:0 10px 30px rgba(0,0,0,0.6)}
    h1{margin:0;font-size:30px}
    p.lead{color:var(--muted);margin:12px 0 22px}
    .row{display:flex;gap:12px;flex-wrap:wrap}
    input,select{padding:12px 14px;border-radius:10px;border:1px solid rgba(255,255,255,0.06);background:transparent;color:var(--white);min-width:0;flex:1}
    button{background:var(--accent);border:none;padding:12px 18px;border-radius:10px;color:#071025;font-weight:700;cursor:pointer}
    .small{font-size:13px;color:var(--muted);margin-top:12px}
    footer{margin-top:18px;color:var(--muted);font-size:13px}
    .hero{display:flex;gap:20px;align-items:center}
    @media(max-width:720px){.hero{flex-direction:column;align-items:flex-start}}
  </style>
</head>
<body>
  <main class="card" role="main">
    <div class="hero">
      <div style="flex:1">
        <h1>Pawnder — Join your city's pack</h1>
        <p class="lead">Tinder + Maps for dogs. Discover dog parks, make playdates, and find trusted breeders & services. Early Bay Area access — join the waitlist.</p>

        <!-- Waitlist form (fires to Netlify forms OR Google Form or Firebase) -->
        <form id="waitlist" onsubmit="onSubmit(event)">
          <div class="row">
            <input id="name" name="name" placeholder="Your name (owner)" required />
            <input id="email" type="email" name="email" placeholder="Email" required />
            <select id="city" name="city" required>
              <option value="">City — pick one</option>
              <option>San Francisco</option>
              <option>Oakland</option>
              <option>Berkeley</option>
              <option>San Jose</option>
              <option>Palo Alto</option>
            </select>
          </div>

          <div style="display:flex;gap:12px;margin-top:12px">
            <input id="dogname" name="dogname" placeholder="Dog's name (optional)" />
            <input id="ref" name="ref" placeholder="Referred by (code)" />
          </div>

          <div style="display:flex;gap:12px;margin-top:14px">
            <button type="submit">Join Waitlist — Get Early Access</button>
            <button id="shareBtn" type="button" onclick="copyShare()" style="background:transparent;border:1px solid rgba(255,255,255,0.08);color:var(--white)">Copy Invite Link</button>
          </div>
        </form>
        <div id="msg" class="small" aria-live="polite"></div>
        <footer>We respect your privacy. No spam. You can delete your info anytime.</footer>
      </div>

      <div style="width:280px;flex:0 0 280px">
        <img src="https://images.unsplash.com/photo-1518717758536-85ae29035b6d?q=80&w=800&auto=format&fit=crop&ixlib=rb-4.0.3&s=placeholder" alt="dog" style="width:100%;border-radius:12px;display:block" />
      </div>
    </div>
  </main>

  <script>
    // Fastest: POST to Google Form or Netlify Forms - replace URL below if using
    const USE_FIRESTORE = false; // set true if you configure Firebase sdk below
    const GOOGLE_FORM_ACTION = ""; // optional: paste Google Form action URL
    async function onSubmit(e){
      e.preventDefault();
      const name = document.getElementById('name').value.trim();
      const email = document.getElementById('email').value.trim();
      const city = document.getElementById('city').value;
      const dog = document.getElementById('dogname').value.trim();
      const ref = document.getElementById('ref').value.trim();
      document.getElementById('msg').textContent = "Saving...";
      try{
        if (GOOGLE_FORM_ACTION){
          // fallback to simple form submit (uncomment if set)
          fetch(GOOGLE_FORM_ACTION, {method:'POST', body: new URLSearchParams({name,email,city,dog,ref})});
          document.getElementById('msg').textContent = "Thanks — you’re on the list!";
          return;
        }
        if (USE_FIRESTORE){
          // firebase must be initialized below
          const doc = await firebase.firestore().collection('waitlist').add({name,email,city,dog,ref,created:firebase.firestore.FieldValue.serverTimestamp()});
          document.getElementById('msg').textContent = "Thanks — you’re on the list!";
          return;
        }
        // Netlify forms simple fallback
        await fetch('/', {method:'POST', headers:{'Content-Type':'application/x-www-form-urlencoded'}, body:`form-name=waitlist&name=${encodeURIComponent(name)}&email=${encodeURIComponent(email)}&city=${encodeURIComponent(city)}&dog=${encodeURIComponent(dog)}&ref=${encodeURIComponent(ref)}`});
        document.getElementById('msg').textContent = "Thanks — you’re on the list!";
      }catch(err){
        console.error(err);
        document.getElementById('msg').textContent = "Couldn’t save. Use the QR on Instagram or DM us.";
      }
    }

    function copyShare(){
      const url = location.href + '?ref=' + (new Date().getTime().toString(36).slice(-6));
      navigator.clipboard.writeText(url).then(()=>{document.getElementById('msg').textContent = "Invite link copied. Share with friends!"});
    }

    // Optional Firebase SDK init placeholder. Replace config with your firebase project's config.
    // <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
    // <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-firestore-compat.js"></script>
    // firebase.initializeApp({ apiKey:"...", authDomain:"...", projectId:"..." });
  </script>
</body>
</html>
