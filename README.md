

### **Day 39: Assertions – Advanced (Concurrent Assertions)**

---

#### **1. Overview**

**Concurrent assertions** in SystemVerilog are used to **monitor temporal properties**, i.e., properties that unfold **over time** across multiple clock cycles. They are extremely powerful for checking **protocols, handshakes, and sequential behavior** in hardware designs.

They differ from **immediate assertions**, which check only a condition at a single point in time.

---

#### **2. Key Concepts**

- **Property**: A sequence or temporal behavior to be observed.
- **Sequence**: Defines a series of events over time.
- **Assertion (assert property)**: Checks whether a property holds true during simulation.

---

#### **3. Syntax Overview**

```systemverilog
property <property_name>;
    <sequence_expression>;
endproperty

assert property (<property_name>)
    else $error("Property failed");
```

Or written inline:

```systemverilog
assert property (@(posedge clk) <sequence_expression>)
    else $error("Assertion failed");
```

---

#### **4. Basic Example**

Let’s say we want to assert that **if `req` is high, `ack` should go high within 2 cycles**:

```systemverilog
assert property (@(posedge clk)
    req |-> ##[1:2] ack
)
else $error("ACK not received within 2 cycles of REQ");
```

- `|->` is the **implication operator** ("if-then")
- `##[1:2]` means “within 1 to 2 clock cycles”

---

#### **5. Sequence and Property Keywords**

- `sequence`: Declares a series of events
- `property`: Declares a property based on a sequence
- `assert property`: Asserts that the property must hold
- `disable iff`: Optional; disables assertion under certain conditions



#### **6. Temporal Operators**

| Operator     | Meaning                                   |
|--------------|--------------------------------------------|
| `##n`        | Delay for `n` clock cycles                 |
| `|->`        | Implication (if LHS true, RHS must happen) |
| `|=>`        | Overlapped implication                     |
| `[*]`        | Repetition (zero or more times)            |
| `[*n]`       | Repetition exactly `n` times               |
| `[*m:n]`     | Repetition between `m` and `n` times       |

---

#### **7. Practical Example: Write-Enable Check**

Say we have a memory write interface with signals:

- `wr_en`: write enable
- `addr`: address
- `data`: data to be written

**Goal**: If `wr_en` is high, then address must be valid (non-zero) and data must be available.

```systemverilog
assert property (@(posedge clk)
    wr_en |-> (addr != 0 && data !== 'x)
)
else $error("Invalid write operation detected");
```

---

#### **8. `disable iff` Usage**

Sometimes you want to ignore the assertion check during reset:

```systemverilog
assert property (@(posedge clk)
    disable iff (rst)
    wr_en |-> (addr != 0)
)
else $error("Address invalid on write enable");
```

---

#### **9. Summary of Benefits**

- **Monitors time-based behavior**
- Ideal for **protocol checks**
- Can track **multi-cycle dependencies**
- Improves **design robustness**
- Helps enforce **design specifications**

---

#### **10. Summary**

| Feature               | Immediate Assertion     | Concurrent Assertion         |
|-----------------------|-------------------------|-------------------------------|
| Timing                | Single time point       | Multiple time points          |
| Context               | Procedural code         | Clocked/Timed logic           |
| Use Case              | Value checks            | Protocols, handshakes, delays |
| Syntax Example        | `assert (a == b)`       | `assert property (@clk ...)`  |

---

Let me know when you're ready to continue with **Day 40: Interface Basics in SystemVerilog**.
