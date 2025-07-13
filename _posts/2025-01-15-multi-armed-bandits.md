---
layout: post
title: "Multi-Armed Bandits"
date: 2025-07-13
categories: [Machine Learning, Quick Commerce]
tags: [bandit-algorithms, search-optimization, personalization, quick-commerce, machine-learning]
author: Srikanth Malyala
math: true
---

I first heard about multi-armed bandits from a friend working at a quick commerce company, who described how they were using these algorithms to optimize their search results in real-time. Intrigued by this concept, I started reading about it and used deep research to curate some important information to understand the concept and its usage. This post explores how Multi-Armed Bandit algorithms can revolutionize search personalization in modern quick commerce platforms.

## Part 1: The Quick Commerce Search Dilemma

Modern quick commerce platforms face an increasingly complex challenge: delivering personalized search experiences that balance user intent with business objectives while maintaining the speed that defines the category. Whether it's a 10-minute delivery service with thousands of SKUs or a specialized quick retailer with a curated catalog, a poorly understood query or irrelevant results can mean the difference between a successful purchase and an abandoned session, making search performance a direct driver of business success.

### The Dual Objective of Search

The challenge for modern quick commerce search engines is serving two distinct and often conflicting strategic goals:

1. **Relevance Optimization**: For users with clear intent (searching for "iPhone 15" or "running shoes"), the search engine should deliver the most relevant items with maximum speed and minimum cognitive load.

2. **Discovery and Revenue Optimization**: To increase Average Order Value (AOV) and customer lifetime value, the search engine must encourage customers to discover complementary products, higher-margin items, or new arrivals that align with their preferences.

### The Inadequacy of Static Ranking

Traditional, static search algorithms are ill-equipped to resolve this inherent tension. A system optimized purely for relevance will excel at the first goal but fail catastrophically at the second, creating a feedback loop where popular items become perpetually more popular while the long tail remains undiscovered.

This leads to the classic **exploration-exploitation dilemma**: should the system show the product it knows the user is likely to buy (exploit), or should it show a different product to gather information and potentially drive a higher-value purchase (explore)?

## Part 2: Foundations of Multi-Armed Bandit Algorithms

Multi-Armed Bandit (MAB) algorithms provide a principled, online learning framework for solving the exploration-exploitation problem. The name originates from a classic thought experiment involving a gambler facing a row of slot machines, each with a different, unknown payout probability.

### Core Concepts

- **Arms (Actions)**: The set of available choices. For quick commerce search, an "arm" is a specific product SKU that could be displayed in a search result slot.
- **Reward**: The feedback received after choosing an arm (e.g., click, purchase, revenue).
- **Regret**: The cumulative opportunity cost incurred by not choosing the optimal arm at each step.

### Key Algorithm Approaches

#### 1. Epsilon-Greedy (ε-Greedy)

The epsilon-greedy algorithm is the most intuitive approach to the exploration-exploitation trade-off. At each decision point, it follows a simple rule:

- With probability **1-ε** (typically 90-95%): **Exploit** by choosing the arm with the highest observed average reward
- With probability **ε** (typically 5-10%): **Explore** by randomly selecting any arm

**How it Works:**
1. **Initialize**: Set all arms with equal expected rewards
2. **Decision**: Generate a random number between 0 and 1
3. **Action**: If random number > ε, choose best arm; otherwise choose randomly
4. **Update**: After observing reward, update the average reward for the chosen arm

**Advantages:**
- Extremely simple to implement and understand
- Guaranteed to eventually find the optimal arm
- Works well when the optimal arm is significantly better than others

**Disadvantages:**
- **Wasteful exploration**: Spends equal time exploring all sub-optimal arms
- **Fixed exploration rate**: Doesn't adapt as we learn more about arms
- **Poor performance**: When many arms exist, random exploration becomes inefficient

**Quick Commerce Example:**
When a user searches for "milk," ε-greedy might show the best-performing milk 90% of the time, but 10% of the time it randomly shows any milk product—even ones that have consistently performed poorly.

#### 2. Upper Confidence Bound (UCB)

UCB implements the principle of "optimism in the face of uncertainty." Instead of random exploration, it systematically prioritizes arms that either perform well or haven't been tested enough.

**The UCB Formula:**
$$UCB_t(a) = \bar{r}_t(a) + c\sqrt{\frac{\ln t}{N_t(a)}}$$

Where:
- $\bar{r}_t(a)$ is the **average reward** for arm $a$ (exploitation term)
- $c\sqrt{\frac{\ln t}{N_t(a)}}$ is the **confidence interval** (exploration bonus)
- $c$ is the confidence parameter (typically 2)
- $t$ is the total number of trials
- $N_t(a)$ is the number of times arm $a$ has been selected

**How it Works:**
1. **Calculate UCB score** for each arm using the formula above
2. **Select the arm** with the highest UCB score
3. **Observe reward** and update statistics
4. **Repeat** the process

**The Exploration Bonus Explained:**
- **High uncertainty** (low $N_t(a)$): Large bonus encourages exploration
- **More trials** (high $t$): Bonus grows slowly, encouraging thorough exploration
- **Well-tested arms** (high $N_t(a)$): Bonus shrinks, focusing on exploitation

**Advantages:**
- **Intelligent exploration**: Prioritizes promising but uncertain arms
- **Theoretical guarantees**: Provable bounds on regret
- **Adaptive**: Automatically balances exploration and exploitation over time

**Disadvantages:**
- **Parameter sensitivity**: Performance depends on choosing the right $c$ value
- **Computational overhead**: Must calculate bounds for all arms at each step
- **Assumes stationary rewards**: Struggles when arm values change over time

**Quick Commerce Example:**
For a "milk" search, UCB might calculate:
- **Popular Brand A**: High average reward (0.8) but well-tested → UCB = 0.8 + small bonus
- **New Organic Brand**: Moderate reward (0.6) but rarely shown → UCB = 0.6 + large bonus
- UCB chooses the new organic brand to reduce uncertainty, potentially discovering a hidden gem

#### 3. Thompson Sampling

Thompson Sampling takes a fundamentally different, Bayesian approach. Instead of maintaining point estimates, it maintains full probability distributions representing beliefs about each arm's true value.

**Core Concept:**
- **Belief representation**: Each arm has a probability distribution (e.g., Beta distribution for binary rewards)
- **Sampling**: At each step, sample one value from each arm's distribution
- **Selection**: Choose the arm with the highest sampled value
- **Update**: After observing reward, update the chosen arm's distribution

**For Binary Rewards (Click/No-Click):**
- Use **Beta distribution**: Beta(α, β)
- **α**: Number of successes (clicks) + 1
- **β**: Number of failures (no clicks) + 1
- **Initial state**: Beta(1,1) = uniform distribution

**How it Works:**
1. **Initialize**: Each arm starts with Beta(1,1) distribution
2. **Sample**: Draw one random value from each arm's current Beta distribution
3. **Select**: Choose arm with highest sampled value
4. **Update**: If click → increment α; if no click → increment β
5. **Repeat**: Distribution becomes more concentrated around true value

**Mathematical Intuition:**
- **Wide distribution** = high uncertainty → more likely to sample extreme values → more exploration
- **Narrow distribution** = high confidence → samples concentrate around mean → more exploitation
- **Natural balance**: Uncertainty decreases as we gather more data

**Advantages:**
- **Excellent empirical performance**: Often outperforms other methods in practice
- **Natural exploration-exploitation balance**: No hyperparameters to tune
- **Handles uncertainty elegantly**: Probability distributions capture our confidence level
- **Flexible**: Easy to incorporate prior knowledge through initial distributions

**Disadvantages:**
- **Implementation complexity**: Requires understanding of probability distributions
- **Computational cost**: Sampling from distributions is more expensive than simple calculations
- **Less interpretable**: Harder to explain why specific decisions were made

**Quick Commerce Example:**
For "milk" search with three products:
- **Product A**: Beta(45, 5) → mostly clicks, narrow distribution around 0.9
- **Product B**: Beta(20, 30) → mixed results, distribution around 0.4
- **Product C**: Beta(2, 1) → new product, wide distribution

Thompson Sampling samples values like [0.88, 0.41, 0.75] and chooses Product A. But sometimes it might sample [0.85, 0.38, 0.92] and choose Product C, naturally exploring the uncertain new product.

**Why Thompson Sampling Often Wins:**
- **Probability matching**: Explores each arm proportional to the probability it's optimal
- **Information-theoretic optimality**: Efficiently resolves uncertainty about arm values
- **Robust performance**: Works well across diverse problem settings without tuning

<div class="tab-container">
  <div class="tab-buttons">
    <button class="tab-button active" data-tab="comparison">Algorithm Comparison</button>
    <button class="tab-button" data-tab="implementation">Implementation Notes</button>
  </div>
  
  <div class="tab-content active" id="comparison">
    <table>
      <thead>
        <tr>
          <th>Attribute</th>
          <th>ε-Greedy</th>
          <th>UCB</th>
          <th>Thompson Sampling</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><strong>Exploration Strategy</strong></td>
          <td>Random with probability ε</td>
          <td>Deterministic optimism</td>
          <td>Probabilistic sampling</td>
        </tr>
        <tr>
          <td><strong>Key Parameters</strong></td>
          <td>Exploration rate ε</td>
          <td>Confidence parameter c</td>
          <td>Prior distribution parameters</td>
        </tr>
        <tr>
          <td><strong>Computational Cost</strong></td>
          <td>Low</td>
          <td>Low to Moderate</td>
          <td>Moderate</td>
        </tr>
        <tr>
          <td><strong>Primary Advantage</strong></td>
          <td>Simple implementation</td>
          <td>Efficient exploration</td>
          <td>High empirical performance</td>
        </tr>
        <tr>
          <td><strong>Primary Disadvantage</strong></td>
          <td>Inefficient exploration</td>
          <td>Parameter sensitivity</td>
          <td>Implementation complexity</td>
        </tr>
      </tbody>
    </table>
  </div>
  
  <div class="tab-content" id="implementation">
    <h4>Implementation Considerations:</h4>
    <ul>
      <li><strong>ε-Greedy:</strong> Start with ε=0.1, consider decay over time</li>
      <li><strong>UCB:</strong> Typical confidence parameter c=2, requires calculating bounds for all arms</li>
      <li><strong>Thompson Sampling:</strong> Use Beta(1,1) prior for binary rewards, naturally adaptive</li>
    </ul>

    <h4>Performance Notes:</h4>
    <ul>
      <li>Thompson Sampling often performs best in practice</li>
      <li>UCB provides theoretical guarantees</li>
      <li>ε-Greedy serves as a good baseline</li>
    </ul>
  </div>
</div>

## Part 3: Real-World Quick Commerce Example

Let's examine how a multi-armed bandit system works in practice for a quick commerce search scenario.

### Scenario: "Milk" Search on QuickMart

Consider a user searching for "milk" on QuickMart, a 15-minute delivery app. The search engine needs to decide which products to show in the top 3 positions from a catalog of 12 milk products.

#### The Product Catalog
```
Product A: Amul Full Cream Milk (1L) - ₹64
Product B: Mother Dairy Toned Milk (1L) - ₹58  
Product C: Nestle A+ Milk (1L) - ₹72
Product D: Organic Valley Milk (1L) - ₹95
Product E: Amul Lactose Free Milk (1L) - ₹78
Product F: Local Dairy Fresh Milk (1L) - ₹52
... (6 more products)
```

#### Multi-Objective Reward Function

Instead of optimizing for a single metric, QuickMart uses a composite reward function:

$$R = w_1 \cdot \text{Click} + w_2 \cdot \text{Purchase} + w_3 \cdot \text{Revenue} + w_4 \cdot \text{Margin}$$

Where:
- **Click**: Binary (0 or 1) - Did user click the product?
- **Purchase**: Binary (0 or 1) - Did user buy the product?
- **Revenue**: Continuous - Product price if purchased
- **Margin**: Continuous - Profit margin if purchased

**Example weights**: w₁=0.1, w₂=0.4, w₃=0.3, w₄=0.2

#### Thompson Sampling Implementation

For each product position, the system maintains Beta distributions tracking performance:

**Position 1 (Hero slot):**
- **Product A (Amul)**: Beta(450, 50) → α=450 successes, β=50 failures
- **Product B (Mother Dairy)**: Beta(380, 120) → α=380 successes, β=120 failures
- **Product C (Nestle A+)**: Beta(280, 70) → α=280 successes, β=70 failures
- **Product D (Organic Valley)**: Beta(25, 5) → α=25 successes, β=5 failures (new product)

**Real-Time Decision Process:**

1. **Sampling**: System samples from each distribution
   - Product A: 0.89 (sampled from Beta(450,50))
   - Product B: 0.72 (sampled from Beta(380,120))
   - Product C: 0.79 (sampled from Beta(280,70))
   - Product D: 0.91 (sampled from Beta(25,5))

2. **Selection**: Product D wins position 1 due to highest sample (0.91)

3. **User Interaction**: User clicks Product D but doesn't purchase

4. **Reward Calculation**: R = 0.1×1 + 0.4×0 + 0.3×0 + 0.2×0 = 0.1

5. **Update**: Product D's distribution becomes Beta(25.1, 5.0)

#### Dynamic Adaptation Over Time

**Week 1**: System heavily explores Product D (organic milk) due to high uncertainty
- 30% of users shown Product D in position 1
- Discovers: High click rate (0.85) but low purchase rate (0.15) due to price sensitivity

**Week 2**: Product D's true performance becomes clearer
- Distribution: Beta(85, 15) → tighter around 0.85
- System reduces exploration of Product D
- 15% of users shown Product D in position 1

**Week 3**: System learns optimal positioning
- Product A (Amul) dominates position 1 for price-sensitive users
- Product D (Organic) shown to high-value customer segments
- Product C (Nestle A+) emerges as strong performer in position 2


<style>
.tab-container {
  margin: 20px 0;
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
}

.tab-buttons {
  display: flex;
  background-color: #f8f9fa;
  border-bottom: 1px solid #ddd;
}

.tab-button {
  flex: 1;
  padding: 12px 16px;
  border: none;
  background-color: transparent;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.3s ease;
  border-bottom: 3px solid transparent;
}

.tab-button:hover {
  background-color: #e9ecef;
}

.tab-button.active {
  background-color: #fff;
  border-bottom-color: #007bff;
  color: #007bff;
  box-shadow: inset 0 -2px 0 #007bff;
}

.tab-content {
  display: none;
  padding: 20px;
  background-color: #fff;
}

.tab-content.active {
  display: block;
}

.tab-content h4 {
  margin-top: 0;
  margin-bottom: 15px;
  color: #333;
  font-size: 1.1em;
}

.tab-content table {
  width: 100%;
  border-collapse: collapse;
  margin: 15px 0;
}

.tab-content th,
.tab-content td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

.tab-content th {
  background-color: #f8f9fa;
  font-weight: 600;
}

.tab-content ul {
  margin: 0;
  padding-left: 20px;
}

.tab-content li {
  margin-bottom: 8px;
  line-height: 1.5;
}

/* Dark mode support */
[data-mode="dark"] .tab-container {
  border-color: #444;
}

[data-mode="dark"] .tab-buttons {
  background-color: #2d2d2d;
  border-bottom-color: #444;
}

[data-mode="dark"] .tab-button {
  color: #ccc;
}

[data-mode="dark"] .tab-button:hover {
  background-color: #3a3a3a;
  color: #fff;
}

[data-mode="dark"] .tab-button.active {
  background-color: #0d6efd;
  border-bottom-color: #0a58ca;
  box-shadow: inset 0 -2px 0 #0a58ca;
}

[data-mode="dark"] .tab-content {
  background-color: #1a1a1a;
}

[data-mode="dark"] .tab-content th {
  background-color: #2d2d2d;
}

[data-mode="dark"] .tab-content th,
[data-mode="dark"] .tab-content td {
  border-bottom-color: #444;
}
</style>

<script>
document.addEventListener('DOMContentLoaded', function() {
  // Handle tab switching
  document.querySelectorAll('.tab-button').forEach(button => {
    button.addEventListener('click', function() {
      const tabContainer = this.closest('.tab-container');
      const targetTab = this.getAttribute('data-tab');
      
      // Remove active class from all buttons and contents in this container
      tabContainer.querySelectorAll('.tab-button').forEach(btn => btn.classList.remove('active'));
      tabContainer.querySelectorAll('.tab-content').forEach(content => content.classList.remove('active'));
      
      // Add active class to clicked button and corresponding content
      this.classList.add('active');
      tabContainer.querySelector(`#${targetTab}`).classList.add('active');
    });
  });
});
</script> 