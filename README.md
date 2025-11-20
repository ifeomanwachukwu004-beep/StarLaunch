
# StarLaunch – Decentralized Crowdfunding With Governance**

This clarity smart contract implementing a decentralized crowdfunding protocol with built-in governance, milestone extensions, voting, creator withdrawals, and contributor refunds.

The contract enables project creators to launch fundraising campaigns, contributors to fund them, and the community to vote on whether funds should be released once the goal is met. Governance requirements ensure transparent and democratic decision-making before any funds are unlocked.

---

## **✨ Features**

### **🔹 Project Creation**

* Creators can submit new crowdfunding projects.
* Each project includes:

  * Name (max 50 chars)
  * Funding goal
  * Deadline (block height)
* Automatically initializes:

  * Voting period
  * Vote tracking variables
  * Extension tracking

### **🔹 Contributions**

* Anyone can contribute STX to active projects before the deadline.
* A contributor can add multiple contributions.
* Contributions are tracked per project per contributor.

### **🔹 Voting System**

* Only contributors can vote.
* Voting ends at `vote-end-time` (deadline + 7 days).
* Votes are recorded once per contributor.
* A successful vote requires:

  * **≥ 60% yes votes**
  * **≥ 10 total votes**

### **🔹 Fund Withdrawal (Creator)**

* Allowed only if:

  * Goal is reached.
  * Voting period has ended.
  * Vote threshold is met.
* Transfers the project funds to the creator.

### **🔹 Refunds (Contributors)**

* Allowed if the project fails by:

  * Not reaching goal, or
  * Not reaching voting threshold.
* Contributors can claim back their individual contribution.

### **🔹 Project Cancellation**

* Creators can cancel their own project *only before anyone contributes*.

### **🔹 Deadline Extensions**

* Allowed **up to 3 times**.
* Max extension: **30 days**.
* Only allowed if:

  * Raised amount is ≥ 75% of the goal.
  * Creator is requesting.
  * Deadline has not passed.

---

## **📁 Data Structures**

### **Project Struct**

```
{
  name: string,
  creator: principal,
  goal: uint,
  deadline: uint,
  total-raised: uint,
  is-active: bool,
  extensions-used: uint,
  vote-end-time: uint,
  total-votes: uint,
  votes-in-favor: uint
}
```

### **Contributor Record**

```
{ amount: uint }
```

### **Vote Record**

```
{ in-favor: bool }
```

---

## **🧮 Key Constants**

| Constant                           | Purpose                                   |
| ---------------------------------- | ----------------------------------------- |
| `EXTENSION_THRESHOLD = 75%`        | Required raised ratio to extend deadlines |
| `MAX_EXTENSION_DAYS = 30`          | Maximum allowable extension               |
| `VOTING_PERIOD_DAYS = 7`           | Voting window after the deadline          |
| `MIN_VOTE_THRESHOLD_PERCENT = 60%` | Yes-vote requirement                      |
| `MIN_VOTE_COUNT_THRESHOLD = 10`    | Minimum voters                            |

Error codes are provided for all critical failure modes (unauthorized, not found, invalid input, voting threshold not met, etc.)

---

## **📚 Public Functions**

| Function          | Description                                        |
| ----------------- | -------------------------------------------------- |
| `submit-project`  | Create a new crowdfunding project                  |
| `contribute`      | Contribute STX to a project                        |
| `vote`            | Vote on a project’s fund release                   |
| `withdraw-funds`  | Allow creator to claim funds after successful vote |
| `refund`          | Contributors claim refund if project fails         |
| `cancel-project`  | Creator cancels project before contributions       |
| `extend-deadline` | Request deadline extension                         |

---

## **👀 Read-Only Functions**

| Function            | Description                               |
| ------------------- | ----------------------------------------- |
| `get-project`       | Retrieve project details                  |
| `get-contribution`  | View a contributor’s contribution         |
| `get-vote`          | View a voter’s vote                       |
| `get-voting-status` | Returns voting stats and threshold status |

---

## **🚀 How It Works (Flow Overview)**

1. **Creator submits project** → active and open for contributions.
2. **Users contribute STX** → tracked individually.
3. **After deadline**, contributors can **vote** on whether the creator should receive funds.
4. If voting succeeds:
   → **creator withdraws** funds.
5. If voting fails or goal is not met:
   → **contributors refund** their contributions.
6. Optional: creator can **extend deadline** (max 3x) if 75% of the goal is already raised.

---

## **🧪 Integration Notes**

* Designed for **Stacks blockchain** smart contracts in Clarity.
* Uses `stx-transfer?` inside `as-contract` for safe contract-based asset movement.
* All arithmetic uses unsigned ints (uint).
* Block-height–based time calculations assume:
  **144 blocks per day**.

---

