# Quantum Pulse - Integration Flow Diagrams

## Complete System Integration Overview

```
╔══════════════════════════════════════════════════════════════════════════╗
║                        QUANTUM PULSE ECOSYSTEM                            ║
╚══════════════════════════════════════════════════════════════════════════╝

┌─────────────────────────────────────────────────────────────────────────┐
│                         USER INTERACTIONS                                │
└─────────────────────────────────────────────────────────────────────────┘
         │                    │                    │
         │                    │                    │
         ▼                    ▼                    ▼
  ┌──────────┐         ┌──────────┐         ┌──────────┐
  │ Health   │         │ Symptom  │         │ Frequency│
  │   Chat   │         │ Checker  │         │ Healing  │
  └────┬─────┘         └────┬─────┘         └────┬─────┘
       │                    │                     │
       │                    │                     │
       ▼                    ▼                     ▼
  ┌─────────────────────────────────────────────────────┐
  │            REACT FRONTEND LAYER                      │
  │  • Router  • Context Providers  • UI Components     │
  └──────────────────────┬──────────────────────────────┘
                         │
                         │ HTTPS/WebSocket
                         │
         ┌───────────────┴───────────────┐
         │                               │
         ▼                               ▼
  ┌─────────────┐               ┌─────────────┐
  │   Supabase  │               │  External   │
  │   Backend   │               │    APIs     │
  │             │               │  (Gemini)   │
  │ • Database  │               └─────────────┘
  │ • Auth      │
  │ • Functions │
  └─────────────┘
```

---

## Module Integration Map

```
╔══════════════════════════════════════════════════════════════════════════╗
║                         MODULE INTERACTIONS                               ║
╚══════════════════════════════════════════════════════════════════════════╝

┌─────────────────────────────────────────────────────────────────────────┐
│                         FRONTEND MODULES                                 │
└─────────────────────────────────────────────────────────────────────────┘

        ┌──────────────┐
        │  Dashboard   │ ◄──────────────────┐
        └───────┬──────┘                    │
                │                           │
      ┌─────────┼─────────┬─────────┐      │
      │         │         │         │      │
      ▼         ▼         ▼         ▼      │
┌──────────┐┌────────┐┌────────┐┌─────────┴──┐
│ Symptom  ││   ML   ││Frequency││   Chat     │
│ Checker  ││  Eval  ││Generator││            │
└────┬─────┘└───┬────┘└────┬────┘└────┬───────┘
     │          │          │          │
     │          │          │          │
     └──────────┼──────────┴──────────┘
                │
                │ Shared Auth & State
                │
        ┌───────┴────────┐
        │  Auth Context  │
        │ Theme Context  │
        │ Audio Context  │
        └────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                         BACKEND MODULES                                  │
└─────────────────────────────────────────────────────────────────────────┘

        ┌───────────────────────┐
        │   PostgreSQL DB       │
        │                       │
        │  ┌─────────────────┐  │
        │  │   ml_models     │  │
        │  ├─────────────────┤  │
        │  │symptom_predict  │  │
        │  ├─────────────────┤  │
        │  │disease_freq_map │  │
        │  └─────────────────┘  │
        └───────────┬───────────┘
                    │
           ┌────────┴────────┐
           │                 │
           ▼                 ▼
    ┌─────────────┐   ┌─────────────┐
    │ml-train-    │   │ml-predict   │
    │  evaluate   │   │             │
    │             │   │             │
    │ • Train     │   │ • Predict   │
    │ • Evaluate  │   │ • Frequency │
    │ • Metrics   │   │ • Confidence│
    └─────────────┘   └─────────────┘

    Edge Functions (Serverless)
```

---

## ML Module Detailed Integration

```
╔══════════════════════════════════════════════════════════════════════════╗
║            ML MODULE: TRAINING & PREDICTION PIPELINE                      ║
╚══════════════════════════════════════════════════════════════════════════╝

TRAINING PIPELINE
═════════════════

┌─────────────────────────────────────────────────────────────────────────┐
│ Step 1: User Initiates Training                                         │
└─────────────────────────────────────────────────────────────────────────┘
                            │
                            ▼
                    [MLEvaluation.tsx]
                   Click "Train Model"
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ Step 2: Edge Function Receives Request                                  │
└─────────────────────────────────────────────────────────────────────────┘
                            │
                            ▼
                [ml-train-evaluate function]
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
        ▼                   ▼                   ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│Load Dataset │    │Preprocess   │    │Train Model  │
│50 samples   │───►│Binary encode│───►│Decision Tree│
│10 diseases  │    │symptoms     │    │Pattern match│
└─────────────┘    └─────────────┘    └──────┬──────┘
                                              │
                                              ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ Step 3: Model Evaluation                                                 │
└─────────────────────────────────────────────────────────────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
        ▼                   ▼                   ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│Confusion    │    │Per-Class    │    │Sample       │
│Matrix       │    │Metrics      │    │Predictions  │
│10x10        │    │P, R, F1     │    │Test cases   │
└─────────────┘    └─────────────┘    └─────────────┘
        │                   │                   │
        └───────────────────┼───────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ Step 4: Save Results to Database                                        │
└─────────────────────────────────────────────────────────────────────────┘
                            │
                            ▼
                    [ml_models table]
                 INSERT model metadata
              accuracy, precision, recall, f1
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ Step 5: Return Results to Frontend                                      │
└─────────────────────────────────────────────────────────────────────────┘
                            │
                            ▼
                    [MLEvaluation.tsx]
                   Display metrics & charts


PREDICTION PIPELINE
═══════════════════

┌─────────────────────────────────────────────────────────────────────────┐
│ Step 1: User Enters Symptoms                                            │
└─────────────────────────────────────────────────────────────────────────┘
                            │
                            ▼
                  [SymptomChecker.tsx]
                Enter: headache, nausea
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ Step 2: Edge Function Processes Input                                   │
└─────────────────────────────────────────────────────────────────────────┘
                            │
                            ▼
                  [ml-predict function]
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
        ▼                   ▼                   ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│Normalize    │    │Pattern      │    │Calculate    │
│Symptoms     │───►│Matching     │───►│Confidence   │
│lowercase    │    │Score each   │    │Best match   │
└─────────────┘    └─────────────┘    └──────┬──────┘
                                              │
                                              ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ Step 3: Fetch Frequency Mapping                                         │
└─────────────────────────────────────────────────────────────────────────┘
                            │
                            ▼
              [disease_frequency_mapping table]
                   Predicted: "Migraine"
                            │
                            ▼
                   Frequency: "528 Hz"
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ Step 4: Save Prediction                                                 │
└─────────────────────────────────────────────────────────────────────────┘
                            │
                            ▼
              [symptom_predictions table]
          INSERT symptoms, disease, frequency
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ Step 5: Return Prediction                                               │
└─────────────────────────────────────────────────────────────────────────┘
                            │
                            ▼
                  [SymptomChecker.tsx]
                            │
            ┌───────────────┼───────────────┐
            │               │               │
            ▼               ▼               ▼
      ┌──────────┐    ┌──────────┐    ┌──────────┐
      │ Disease  │    │Frequency │    │Benefits  │
      │ Migraine │    │  528 Hz  │    │Pain      │
      │   92%    │    │          │    │reduction │
      └──────────┘    └──────────┘    └──────────┘
```

---

## Frequency Healing Integration

```
╔══════════════════════════════════════════════════════════════════════════╗
║         SYMPTOM CHECKER → FREQUENCY GENERATOR INTEGRATION                 ║
╚══════════════════════════════════════════════════════════════════════════╝

┌─────────────────────────────────────────────────────────────────────────┐
│                     USER JOURNEY FLOW                                    │
└─────────────────────────────────────────────────────────────────────────┘

    [User at Dashboard]
            │
            │ Click "Symptom Checker"
            ▼
    ┌──────────────────┐
    │ Symptom Checker  │
    │                  │
    │ 1. Add symptoms  │
    │    • Headache    │
    │    • Nausea      │
    │    • Light sens  │
    │                  │
    │ 2. Click Analyze │
    └────────┬─────────┘
             │
             │ ML Prediction API Call
             ▼
    ┌──────────────────┐
    │  Prediction      │
    │                  │
    │ Disease: Migraine│
    │ Confidence: 92%  │
    │ Freq: 528 Hz     │
    │                  │
    │ [Go to Frequency │
    │  Generator] ◄────┼─── User clicks
    └────────┬─────────┘
             │
             │ navigate('/frequency-generator')
             ▼
    ┌──────────────────┐
    │ Frequency Gen    │
    │                  │
    │ [528 Hz Preset]  │◄─── Can auto-select
    │                  │     based on prediction
    │ [Play Button]    │
    └────────┬─────────┘
             │
             │ playFrequency(528)
             ▼
    ┌──────────────────┐
    │  Web Audio API   │
    │                  │
    │  Playing 528 Hz  │
    │  Sine Wave       │
    │                  │
    │  ♪♫♪ [========]  │
    └──────────────────┘


TECHNICAL FLOW
══════════════

SymptomChecker.tsx                    FrequencyGenerator.tsx
       │                                      │
       │ Prediction result:                  │
       │ { healing_frequency: "528 Hz" }     │
       │                                     │
       │ Button click:                       │
       │ onClick={() => navigate(            │
       │   '/frequency-generator',           │
       │   { state: { frequency: 528 } }     │
       │ )}                                  │
       │                                     │
       └────────────────────────────────────►│
                                             │
                                             │ useLocation()
                                             │ const { frequency } =
                                             │   location.state || {}
                                             │
                                             │ if (frequency) {
                                             │   setCustomFrequency(frequency)
                                             │   or auto-select preset
                                             │ }
                                             │
                                             ▼
                                    AudioContext.playFrequency()
```

---

## Authentication Flow Integration

```
╔══════════════════════════════════════════════════════════════════════════╗
║                  AUTHENTICATION & AUTHORIZATION FLOW                      ║
╚══════════════════════════════════════════════════════════════════════════╝

SIGNUP/LOGIN FLOW
═════════════════

    [Login/Signup Page]
            │
            │ User enters email & password
            ▼
    ┌──────────────────┐
    │  AuthContext     │
    │                  │
    │  signIn() or     │
    │  signUp()        │
    └────────┬─────────┘
             │
             │ supabase.auth.signInWithPassword()
             ▼
    ┌──────────────────┐
    │  Supabase Auth   │
    │  (GoTrue)        │
    │                  │
    │  Verify creds    │
    │  Generate JWT    │
    └────────┬─────────┘
             │
             │ Return session
             ▼
    ┌──────────────────┐
    │  AuthContext     │
    │                  │
    │  setUser(user)   │
    │  Save token      │
    └────────┬─────────┘
             │
             │ navigate('/dashboard')
             ▼
    [Protected Routes Unlocked]


PROTECTED ROUTE ACCESS
══════════════════════

    User navigates to /symptom-checker
            │
            ▼
    ┌──────────────────┐
    │ ProtectedRoute   │
    │  Wrapper         │
    │                  │
    │  if (!user)      │
    │    redirect      │
    │    to /login     │
    │  else            │
    │    render page   │
    └────────┬─────────┘
             │
             │ User authenticated
             ▼
    ┌──────────────────┐
    │ Symptom Checker  │
    │  Component       │
    │                  │
    │  useAuth()       │
    │  const { user }  │
    └────────┬─────────┘
             │
             │ Make API call with JWT
             ▼
    POST /functions/v1/ml-predict
    Headers: {
      Authorization: Bearer <JWT>
    }
    Body: {
      symptoms: [...],
      user_id: user.id  ◄─── Linked to user
    }
            │
            ▼
    [Prediction saved with user_id]
```

---

## Database Integration & RLS

```
╔══════════════════════════════════════════════════════════════════════════╗
║                   DATABASE ACCESS PATTERNS                                ║
╚══════════════════════════════════════════════════════════════════════════╝

EDGE FUNCTION → DATABASE
════════════════════════

    [Edge Function: ml-predict]
             │
             │ Import Supabase client
             ▼
    const supabase = createClient(
      SUPABASE_URL,
      SUPABASE_ANON_KEY
    )
             │
             │ Query with automatic RLS
             ▼
    const { data } = await supabase
      .from('disease_frequency_mapping')
      .select('*')
      .eq('disease_name', 'Migraine')
      .single()
             │
             │ RLS Policy Applied:
             │ "Anyone can view frequency mappings"
             ▼
    [Returns frequency: "528 Hz"]


ROW LEVEL SECURITY
══════════════════

┌─────────────────────────────────────────────────────────────────────────┐
│                      RLS Policy Enforcement                              │
└─────────────────────────────────────────────────────────────────────────┘

ml_models table
├─ SELECT: Public (anyone can view)
├─ INSERT: Service role only
└─ UPDATE: Service role only

symptom_predictions table
├─ SELECT: Authenticated (own data) OR anonymous (if no user_id)
├─ INSERT: Public (anyone can create)
└─ UPDATE: Service role only

disease_frequency_mapping table
├─ SELECT: Public (anyone can view)
└─ ALL: Service role only


QUERY FLOW WITH RLS
═══════════════════

    Authenticated User Query
             │
             │ JWT contains: { sub: "user-uuid" }
             ▼
    SELECT * FROM symptom_predictions
    WHERE user_id = auth.uid()
             │
             │ RLS automatically adds:
             │ AND user_id = 'user-uuid'
             ▼
    [Returns only user's predictions]


    Service Role Query
             │
             │ Uses service_role_key
             ▼
    INSERT INTO ml_models (...)
    VALUES (...)
             │
             │ RLS bypassed (service role)
             ▼
    [Insert succeeds]
```

---

## Complete Data Flow Example

```
╔══════════════════════════════════════════════════════════════════════════╗
║           END-TO-END EXAMPLE: SYMPTOM TO HEALING SESSION                 ║
╚══════════════════════════════════════════════════════════════════════════╝

USER: "I have a headache and nausea"
│
├─ STEP 1: User Input
│  └─ Navigate to /symptom-checker
│  └─ Enter symptoms: ["headache", "nausea", "light sensitivity"]
│  └─ Click "Analyze Symptoms"
│
├─ STEP 2: ML Prediction
│  │
│  ├─ Frontend: SymptomChecker.tsx
│  │  └─ POST /functions/v1/ml-predict
│  │     Body: { symptoms: [...], user_id: "abc-123" }
│  │
│  ├─ Backend: ml-predict function
│  │  ├─ Normalize symptoms
│  │  ├─ Pattern matching against disease signatures
│  │  ├─ Calculate scores: Migraine (0.92), Stress (0.45)
│  │  └─ Best match: Migraine (92% confidence)
│  │
│  ├─ Database: Fetch frequency
│  │  └─ SELECT FROM disease_frequency_mapping
│  │     WHERE disease_name = 'Migraine'
│  │     Returns: { frequency: "528 Hz", benefits: [...] }
│  │
│  ├─ Database: Save prediction
│  │  └─ INSERT INTO symptom_predictions
│  │     VALUES (symptoms, 'Migraine', 0.92, '528 Hz', 'abc-123')
│  │
│  └─ Response to Frontend
│     └─ {
│           disease: "Migraine",
│           confidence: 0.92,
│           healing_frequency: "528 Hz",
│           benefits: ["Pain reduction", "Stress relief"]
│        }
│
├─ STEP 3: Display Prediction
│  │
│  └─ Frontend: SymptomChecker.tsx
│     ├─ Show disease: Migraine
│     ├─ Show confidence: 92%
│     ├─ Show frequency: 528 Hz
│     ├─ List benefits
│     └─ Button: "Go to Frequency Generator"
│
├─ STEP 4: Navigate to Healing
│  │
│  └─ User clicks button
│     └─ navigate('/frequency-generator', {
│          state: { frequency: 528 }
│        })
│
├─ STEP 5: Play Healing Frequency
│  │
│  ├─ Frontend: FrequencyGenerator.tsx
│  │  ├─ Receive frequency: 528 from state
│  │  ├─ User selects "DNA Repair (528 Hz)" preset
│  │  └─ Click "Play"
│  │
│  ├─ AudioContext: playFrequency(528, 900)
│  │  ├─ Create AudioContext
│  │  ├─ Create OscillatorNode (sine wave, 528 Hz)
│  │  ├─ Create GainNode (volume control)
│  │  ├─ Connect: Oscillator → Gain → Destination
│  │  ├─ Start playing
│  │  └─ Auto-stop after 15 minutes
│  │
│  └─ Audio Output
│     └─ 🎵 Playing 528 Hz sine wave 🎵
│
└─ STEP 6: Track Session (Optional)
   │
   └─ Database: Save session history
      └─ INSERT INTO user_sessions
         VALUES (user_id, 'frequency_healing', 528, 900, timestamp)


RESULT: User receives personalized healing frequency based on AI prediction
```

---

## Summary of Integration Points

```
╔══════════════════════════════════════════════════════════════════════════╗
║                    KEY INTEGRATION POINTS                                 ║
╚══════════════════════════════════════════════════════════════════════════╝

1. ML MODULE ↔ FREQUENCY GENERATOR
   └─ Disease prediction → Frequency recommendation → Audio playback

2. AUTHENTICATION ↔ ALL MODULES
   └─ User context → Protected routes → Personalized data

3. DATABASE ↔ EDGE FUNCTIONS
   └─ RLS policies → Secure data access → Automatic filtering

4. FRONTEND ↔ BACKEND
   └─ REST API → JSON responses → State updates

5. CONTEXT PROVIDERS ↔ COMPONENTS
   └─ Global state → Cross-component communication → Consistency

6. ROUTER ↔ PAGES
   └─ Navigation → State passing → Deep linking

7. EDGE FUNCTIONS ↔ EXTERNAL APIs
   └─ Gemini API → Supabase client → Third-party services
```

---

This integration architecture ensures:
✅ Seamless user experience across all features
✅ Secure data handling with multi-layer protection
✅ Scalable serverless architecture
✅ Modular design for easy maintenance
✅ Clear separation of concerns
✅ Real-time responsiveness
