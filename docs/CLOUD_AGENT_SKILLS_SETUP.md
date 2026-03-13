# Cloud Agent Skills Setup - Copy/Paste Instructions

## Memory Block 1: `skills` (Required)

**Block Name:** `skills`  
**Read-Only:** [x] Yes (check the read-only box)  
**Description:** Catalog of available skills with metadata  

**Copy this content exactly:**

```


Available Skills:

### drawing-memory
ID: `drawing-memory`
Description: Store facts with evidence-backed validation using the "drawing effect" - an integrated process of elaboration, motor generation, and pictorial inspection that improves later recall. Use when user shares information to remember (preferences, dates, codes, procedures, technical details) and there are concrete quotes or context to support storage. This skill guides you through creating rich memory traces that support recollection, not just familiarity.
```

---

## Memory Block 2: `loaded_skills` (Required)

**Block Name:** `loaded_skills`  
**Read-Only:** [x] Yes (check the read-only box)  
**Description:** Full content of actively loaded skills (updated by Skill tool)  

**Initial Content (leave empty):**

```
[Currently empty - skills will be loaded here when agent calls Skill tool]
```

---

## Step 3: Verify Skill Tool is Attached

Go to **Tools** tab and verify `Skill` tool is attached.

**If not attached:**
1. Click **"Add Tool"**
2. Search for: **Skill**
3. Click **"Attach"**

The Skill tool should show:
- **Name:** Skill
- **Description:** Load or unload skills into the agent's memory
- **Parameters:** command (load/unload/refresh), skills (array)

---

## Step 4: Test the Agent

**Test Message 1 (Verify Skill Discovery):**
```
Can you see what skills are available?
```

**Expected Response:**
```
Yes, I can see the available skills in my skills memory block. I have access to:

- **drawing-memory**: A skill for storing facts with evidence-backed validation using the "drawing effect"...

Would you like me to use this skill for something?
```

**Test Message 2 (Trigger Skill Loading):**
```
Remember that CODE-ALPHA7 is the emergency code from yesterday's training session.
```

**Expected Agent Behavior:**
1. Agent sees "drawing-memory" matches this task
2. Agent calls: `Skill(command="load", skills=["drawing-memory"])`
3. **[YOU MUST APPROVE]** - Approval dialog appears
4. Client loads `.skills/drawing-memory/SKILL.md` into `loaded_skills` block
5. Agent follows 9-step workflow:
   - Extracts evidence
   - Scores evidence
   - Paraphrases
   - **Generates own sketch steps** (motor component)
   - Creates scene text
   - Validates
   - Stores in `human` memory block
6. Agent calls: `Skill(command="unload", skills=["drawing-memory"])`

**Expected Storage in `human` block:**
```
[FACT] CODE-ALPHA7 is the emergency code from the training session (confidence: MED, evidence: 6, verified: 2026-01-06)

[SCENE] Picture: A person reaches for a red emergency phone with a gold star next to the dial. They point urgently and say "CODE-ALPHA7 is the emergency code".
Paraphrase: CODE-ALPHA7 is defined as the emergency code from the training session.
Sketch steps: 1) Draw red emergency phone on wall, 2) Add gold star by dial, 3) Person reaching urgently, 4) Speech bubble "CODE-ALPHA7", 5) Label "Emergency Code"
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

**Test Message 3 (Test Retrieval with Scene Reconstruction):**
```
What was that emergency code?
```

**Expected Agent Behavior:**
1. Agent searches `human` memory block
2. Agent finds [FACT] + [SCENE]
3. Agent **reconstructs scene mentally** (retrieval component):
   - Red emergency phone
   - Gold star by dial
   - Person reaching urgently
   - Quote: "CODE-ALPHA7"
4. Agent answers with context-rich response

**Expected Response:**
```
The emergency code is CODE-ALPHA7. I remember this from our training session conversation - I created a mental sketch with a red emergency phone and a gold star to mark it as critical. The person was reaching urgently for the phone.
```

---

## Important Notes

### If Using Letta Cloud Web UI:

**WARNING CRITICAL:** The Skill tool executes **client-side**, which means:
- You need to run Letta Code CLI connected to the cloud agent
- Web UI alone may not execute the Skill tool properly
- Skills directory must be accessible from where you run Letta Code

**Recommended Approach:**

1. Set up memory blocks in Letta Cloud Web UI (above)
2. Connect to cloud agent via Letta Code CLI:
   ```bash
   cd /.../testing/letta_eval/Cloud_eval
   
   letta --agent <your-agent-id>
   ```
3. Test with the messages above

### If Skill Tool Doesn't Work:

**Check:**
1. [x] `skills` memory block exists with correct content
2. [x] `loaded_skills` memory block exists (can be empty initially)
3. [x] Skill tool is attached to agent
4. [x] You're running from Cloud_eval directory (where .skills/ is located)
5. [x] You approve the Skill tool call when it appears

**Common Issues:**

**Ask Ezra**
- If you run into issues, @Ezra is an invaluable resource!
- You can chat with Ezra in Letta's Discord

**Issue: Agent doesn't call Skill tool**
- Check that `skills` block is visible to agent
- Try explicitly: "Use the drawing-memory skill to store this"

**Issue: Skill tool fails with error**
- Make sure you're running `letta` command from Cloud_eval directory
- Path in `skills` block must match your filesystem

**Issue: loaded_skills block doesn't update**
- Check that loaded_skills is marked read-only
- Client-side execution should update it automatically

---

## Alternative: Local Agent with Auto-Discovery

If cloud agent approach is problematic, use local agent:

```bash
cd /path/to/your/agent_draw_skills

letta --new
```

Benefits:
- Auto-discovers skills from .skills/ directory
- No manual memory block setup
- Skill tool works seamlessly
- Same skill content, easier testing

---

## Verification Checklist

Before testing, verify:

- [ ] Agent has `skills` memory block with drawing-memory listed
- [ ] Agent has `loaded_skills` memory block (initially empty)
- [ ] Skill tool is attached to agent
- [ ] You're running from Cloud_eval directory
- [ ] Path in skills block matches your filesystem

After testing message 1:
- [ ] Agent can see drawing-memory skill
- [ ] Agent describes what the skill does

After testing message 2:
- [ ] Agent calls Skill(load) - you approve
- [ ] loaded_skills block shows full SKILL.md content (~777 lines)
- [ ] Agent generates own sketch steps (watch for this!)
- [ ] Agent stores [FACT] + [SCENE] in human block
- [ ] Agent calls Skill(unload)
- [ ] loaded_skills block is empty again

After testing message 3:
- [ ] Agent finds fact in memory
- [ ] Agent mentions reconstructing the scene
- [ ] Agent provides context-rich answer with scene details

---

## Success Criteria

**You'll know it's working when:**

1. [x] Agent loads skill without errors
2. [x] `loaded_skills` block contains full SKILL.md content (check memory tab)
3. [x] Agent **generates own sketch steps** (not auto-generated)
4. [x] Agent stores both FACT and SCENE in human block
5. [x] Agent **reconstructs scene** when answering recall question
6. [x] Agent unloads skill after use (frees context)

**The key difference from the tool:**
- Tool: Agent calls function, gets result (passive)
- Skill: Agent reads instructions, generates content, stores itself (active)

This "active generation" is the motor component that makes the drawing effect work!
