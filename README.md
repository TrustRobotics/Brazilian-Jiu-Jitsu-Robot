# RFC: BJJ Robot — Interactive Brazilian Jiu-Jitsu for Humanoid Robots

**Status:** Draft
**Project:** Trust Robotics BJJ Robot
**Related Patent:** DOR-282
**Title:** Brazilian Jiu-Jitsu Humanoid Robot Physical Action Control


## White Paper

- [PDF edition](docs/white-paper/DOR-282_Brazilian_Jiu-Jitsu_Robot_White_Paper.pdf) — browser-readable
- [Word edition](docs/white-paper/DOR-282_Brazilian_Jiu-Jitsu_Robot_White_Paper.docx) — editable source

## 1. Abstract

This RFC proposes an architecture for a humanoid robot capable of interactive Brazilian jiu-jitsu (BJJ) with a human or another humanoid robot.

The BJJ Robot combines multimodal perception with a BJJ interaction model that determines the state of an ongoing grappling interaction. The state may include BJJ positions, techniques, technique states, physical relationships between respective body regions, interaction history, and participant responses.

A BJJ physical action generation model uses this information to generate physical actions. During execution, the robot continues sensing the interaction, updates its state, and generates subsequent actions in response to the opponent.

The architecture supports BJJ specific decision policies, practitioner personas, configurable aggressiveness and tap sensitivity, submission recognition, robot tap out behavior, and optional world or physics models. A vision-language-action (VLA) model is one possible physical action generation pathway but is not required.

## 2. Motivation

BJJ presents a difficult physical intelligence problem because two independently acting bodies maintain multiple simultaneous and rapidly changing physical relationships.

A robot may need to understand that:

- its hand grips an opponent's wrist;
- its forearm forms a frame;
- its leg provides a hook;
- the opponent has established an underhook;
- the opponent's hand provides a post;
- weight is being transferred through particular body regions; and
- an opponent response has created an entry condition for a different technique.

Image recognition alone is insufficient. Much of the relevant interaction may be occluded, and identical semantic positions may have materially different physical configurations.

The objective is therefore not merely to recognize BJJ positions. It is to create a humanoid capable of **closed loop physical reasoning and action during BJJ**.

## 3. Goals

The BJJ Robot SHOULD:

1. interact physically with a human or another humanoid during BJJ;
2. combine camera, tactile, force, torque, pressure, proprioceptive, inertial, audio, and other available perception;
3. determine BJJ positions, techniques, technique states, and participant responses;
4. represent changing physical relationships between body regions;
5. generate BJJ physical actions from the current interaction state;
6. continuously update the interaction state during execution;
7. recognize opponent responses and transition among techniques;
8. recognize opponent taps and verbal submissions;
9. determine when the robot itself should tap out;
10. support configurable aggressiveness and tap sensitivity;
11. support practitioner, instructor, academy, lineage, or synthetic BJJ personas; and
12. enforce physical safety independently of competitive behavior.

The architecture SHOULD permit implementations without a VLA, world model, physics model, named BJJ position classifier, or explicit relational graph.

## 4. System Architecture

The reference architecture contains four principal runtime components:

**Sensor System 110 → BJJ Inter-Body Relational State Engine 120 → BJJ Physical Action Generation Model 130 → Actuator Control System 140**

The opponent may be a human participant or another humanoid robot.

### 4.1 Sensor System

Sensor System 110 collects available information concerning the robot, opponent, and environment.

Inputs MAY include:

- RGB or depth cameras;
- tactile sensors;
- force and torque sensors;
- pressure sensors;
- joint state and proprioception;
- IMUs;
- proximity sensing; and
- microphones.

Multimodal sensing is particularly important because grappling frequently occludes relevant body regions.

### 4.2 BJJ Interaction Model

A BJJ interaction model processes sensor information to determine a machine usable state of the BJJ interaction.

The state MAY represent:

- BJJ position;
- BJJ technique;
- technique progression;
- participant response;
- interaction history;
- safety state;
- inter-body relational state; and
- physical or biomechanical conditions.

The interaction model SHOULD not require every implementation to use a named BJJ position.

### 4.3 Inter-Body Relational State

An optional but preferred representation describes relationships between body regions of the robot and opponent.

Relationships MAY include:

**contact, grip, hook, frame, post, support, load, pin, constraint, entanglement, control, force transmission, relative orientation, and relative movement.**

A relational state MAY be represented as a graph, hypergraph, matrix, tensor, structured state, embedding, token representation, latent representation, or other machine usable structure.

This representation allows the system to reason below the semantic level of conventional position names.

For example:

> `ROBOT_LEFT_HAND — GRIP → OPPONENT_RIGHT_WRIST`

and

> `OPPONENT_LEFT_HAND — POST → MAT`

may be more actionable than simply classifying the interaction as “guard.”

## 5. BJJ Positions

The initial positional ontology SHOULD support at least:

- standing;
- guard;
- side control;
- mount; and
- back mount.

Additional states SHOULD include closed guard, open guard, half guard, turtle, knee-on-belly, north-south, front headlock, clinch, and leg entanglement.

Positions are abstractions over physical relationships. The underlying relational state SHOULD remain available even when a named position is determined.

## 6. Technique Representation

The initial technique library SHOULD support takedowns, guard pulls, guard passes, sweeps, reversals, escapes, positional transitions, armbars, chokes, triangles, omoplatas, foot locks, leg locks, controls, counters, defenses, and submissions.

A technique SHOULD be capable of representation as a transformation:

**Entry Relational State → Intermediate Relational State(s) → Target Relational State**

This allows execution to be evaluated according to physical results rather than reproduction of a fixed human trajectory.

For example, a sweep may seek to:

**control body regions → remove a post/base → redirect weight → rotate/translate opponent → establish top control.**

The precise robot joint trajectory may differ from the trajectory demonstrated by a human practitioner.

## 7. BJJ Physical Action Generation

BJJ Physical Action Generation Model 130 receives BJJ specific machine information and generates a physical action.

The model MAY generate:

- a technique;
- technique component;
- physical action token;
- action primitive;
- movement;
- trajectory;
- target pose;
- force or torque objective;
- control objective; or
- actuator level information.

A VLA model is **one implementation** of the physical action generation pathway.

For example:

**Perception → BJJ Interaction Model → BJJ Specific Machine Information → VLA → Physical Action Tokens**

Other implementations MAY use learned policies, transformers, reinforcement learning policies, planners, neural networks, decision architectures, or combinations thereof.

## 8. Continuous Interaction Loop

BJJ is modeled as a continuing physical exchange rather than a sequence of isolated commands.

A representative loop is:

**Opponent action → Sense → Determine BJJ state → Generate robot action → Execute → Opponent response → Sense again**

The second cycle uses the physical state produced by the first.

Accordingly:

**Actionₙ → Opponent Responseₙ → Stateₙ₊₁ → Actionₙ₊₁**

This process MAY continue throughout a roll.

An opponent's defense to one technique may itself establish the entry conditions for another technique.

## 9. Technique Decision Architecture

The robot MAY maintain a BJJ technique decision architecture associating:

**Current State → Objective → Available Techniques → Anticipated Response → Counter/Transition → Resulting State**

The architecture MAY be implemented as a decision graph, policy, neural model, state transition system, technique library, probabilistic model, rules structure, or hybrid.

Participant response prediction MAY be used but is not mandatory.

## 10. World and Physics Models

A world or physics model is optional.

When present, it MAY represent:

- center of mass;
- base of support;
- momentum;
- force;
- torque;
- leverage;
- friction;
- pressure;
- weight and load distribution;
- joint loading;
- range of motion;
- balance;
- participant strength; and
- robot actuator capability.

Candidate techniques MAY be evaluated according to predicted future physical or relational states.

The system MUST NOT require a physics model merely to generate a BJJ action.

## 11. Tap and Submission Protocol

Submission behavior is a first-class part of the BJJ Robot protocol.

### 11.1 Opponent Tap

The robot SHOULD detect submission signals including:

- physical tapping of the robot;
- tapping the mat;
- repeated tapping;
- verbal “tap” or another submission indication;
- designated gestures; and
- other configured submission signals.

Submission detection SHOULD preempt ordinary technique execution.

The robot SHOULD release or reduce the relevant physical action promptly.

### 11.2 Robot Tap

A humanoid robot SHOULD be capable of tapping out even when the submission cannot mechanically damage it.

For example, if a human correctly establishes an armbar that would cause a human practitioner to submit, the robot SHOULD be capable of recognizing the successful technique and conceding rather than exploiting stronger mechanical joints.

Robot submission MAY be communicated by:

- physically tapping the opponent;
- tapping the mat;
- verbal output;
- an electronic signal;
- cessation of resistance; or
- another agreed signal.

## 12. Tap Sensitivity and Aggressiveness

The robot SHOULD expose independent behavioral parameters for **tap sensitivity** and **aggressiveness**.

Tap sensitivity controls when the robot concedes a recognized submission.

Aggressiveness MAY affect attack frequency, pace, pressure, technique persistence, willingness to transition, positional risk, and preference for control versus attack.

These parameters SHOULD be independent.

For example:

`Aggressiveness: 8/10`
`Tap Sensitivity: 9/10`

could produce an attacking robot that nevertheless taps conservatively.

Safety limits MUST supersede both settings.

## 13. Persona Policy Cards

The system MAY support downloadable or selectable **persona policy cards**.

A persona policy card represents machine usable BJJ characteristics associated with a practitioner, instructor, academy, lineage, methodology, skill level, or synthetic persona.

A policy card MAY specify:

- positional priorities;
- preferred techniques;
- technique chains;
- response mappings;
- timing preferences;
- pressure preferences;
- aggressiveness;
- tap sensitivity;
- decision rules;
- embeddings;
- adapters;
- model parameters; and
- technique weights.

For example, different policy cards could represent BJJ characteristics associated with **Ralph Gracie** or **Renzo Gracie**.

Loading a different policy card may cause the same humanoid hardware to make different decisions from substantially identical BJJ states.

## 14. Direct Practitioner Training

A practitioner MAY train a persona directly.

Training MAY include:

- demonstrating techniques;
- rolling with the robot;
- correcting actions;
- verbal instruction;
- identifying positions and techniques;
- choosing preferred actions;
- rejecting inappropriate actions;
- teleoperation; and
- adjusting technique priorities.

The resulting demonstrations, relational state sequences, decisions, corrections, and outcomes MAY train or update the corresponding persona policy.

The objective is not necessarily to reproduce the practitioner's precise movements. The system may instead learn the practitioner's **decision relationships**.

## 15. Safety

Competitive BJJ behavior and physical safety MUST be separable.

The safety layer SHOULD be capable of limiting or terminating an action based on force, torque, pressure, joint state, biomechanical condition, participant distress, or another safety condition regardless of the active persona, aggressiveness, or tap sensitivity.

A persona MUST NOT override a mandatory safety constraint.

## 16. Training Modes

The system MAY support:

**Instruction Mode** — robot demonstrates or teaches a technique.

**Drilling Mode** — robot repeatedly provides a configured reaction or position.

**Adaptive Training Mode** — robot changes resistance and reactions according to student performance.

**Rolling Mode** — continuous interactive BJJ.

**Competition Simulation Mode** — robot applies a selected ruleset, skill level, persona, and strategy.

**Robot-v-Robot Mode** — humanoids train through self play or competitive interaction.

## 17. Extension Beyond BJJ

The same physical interaction architecture MAY support no-gi grappling, wrestling, judo, sambo, MMA, and other contact rich physical interactions.

The underlying principle is broader:

> A humanoid should reason about the changing relationships between bodies, not merely recognize the bodies as objects.

BJJ provides a demanding environment in which to develop and test that capability.

## 18. Open Research Questions

Important areas for experimentation include how best to encode relational states; how much BJJ knowledge should reside in the interaction model versus the action model; whether technique transformations transfer efficiently from humans to humanoids; how tactile information should be fused with vision; how persona policies should be learned and validated; how submission states should be recognized; and how safety constraints should interact with learned physical policies.

## 19. Proposed Initial Milestone

A useful first demonstration does not require championship-level robotic BJJ.

The initial milestone should demonstrate a humanoid that can:

**recognize five fundamental BJJ positions → maintain a multimodal interaction state → recognize several techniques → choose context appropriate actions → respond to a human counter → update its state → select a subsequent action → recognize a tap → release safely → recognize when it has been caught → tap out.**

Achieving that loop would demonstrate something more fundamental than robotic martial arts.

It would demonstrate a humanoid capable of **reasoning and acting through continuously changing physical relationships with another intelligent body**.