
### Relative drop chances
Below is a table that shows each orb's relative drop weights.

| Orb               | Drop weight | % drop chance |
| ----------------- | ----------- | ------------- |
| Transmutation Orb | 3000        | $\sim 15.5\%$ |
| Augmentation Orb  | 3000        | $\sim 15.5\%$ |
| Alteration Orb    | 4000        | $\sim 20.7\%$ |
| Regal Orb         | 1000        | $\sim 5.1\%$  |
| Alchemy Orb       | 2500        | $\sim 12.9\%$ |
| Exalted Orb       | 300         | $\sim 1.5\%$  |
| Divine Orb        | 100         | $\sim 0.5\%$  |
| Chaos Orb         | 2500        | $\sim 12.9\%$ |
| Scouring Orb      | 2500        | $\sim 12.9\%$ |
| Orb of Annulment  | 400         | $\sim 2\%$    |
| **Sum weight**    | 19300       | $\sim 100\%$  |
#### In general
> Higher IIQ gives more drops
> Higher IIR gives rarer drops
> Rolls are independent of each other
### How are loot generated
We use a few data points to decide if the player should be rewarded loot, and how much:
1. Level difference between the player and the creature
2. The relative power of the creature
3. Item Quantity and Item Rarity on the player's equipment / buffs

>_When levelling, your level is taken into account when generating equipment loot. Example: At level 8, equipment dropped are specifically for level 8 characters._

>_Once you hit level 80, all equipment will be for level 80, and Item Level will be used to determine the drops instead._

By default, all monsters have an implicit "drop" chance. This chance can then be modified by the modifiers above.

$$
\text{BaseChance} = \begin{cases} 0\% & \text{if } \text{Mob Level} \leq \text{Player Level} - 5 \\ 2\% & \text{if } \text{Mob Level} = \text{Player Level} - 4 \\ 3\% & \text{if } \text{Mob Level} = \text{Player Level} - 3 \\ 10\% & \text{if } \text{Mob Level} \geq \text{Player Level} - 2 \end{cases}
$$
### Power Multiplier
The power multiplier is a value that is used to determine the relative power of the creature:
$$
\text{PowerMultiplier} = \begin{cases} 1 \times & \text{if } \text{Mob Rarity} = \text{Common} \\ 2 \times & \text{if } \text{Mob Rarity} = \text{Magic} \\ 4 \times & \text{if } \text{Mob Rarity} = \text{Rare} \\ 8 \times & \text{if } \text{Mob Rarity} = \text{Unique} \end{cases}
$$
### Increased Item Quantity influence
The player can have increased item quantity on their equipment, and this will increase the chance of getting additional loot. The formula is as follows:

For **any given drop**, the probability of stopping is:
$$P_{stop} = 1 - P_{\text{extra drop}}$$
Using the formula:
$$P_{\text{extra drop}} = 1 - \frac{1}{1 + IIQ / 100}$$
We get:
$$P_{stop} = \frac{1}{1 + IIQ / 100}$$
- At 0% IIQ → $P_{stop} = 1$ (Always stop after 1 drop)
- At 100% IIQ → $P_{stop} = 0.5$ (Each drop has a 50% chance to stop the chain)
- At 500% IIQ → $P_{stop} = 0.166$ (16.6% chance to stop the chain)

### Increased Item Rarity (for orb drops)
The player can have increased item rarity on their equipment, and this will increase the chance of getting better loot. The formula is as follows:

$$W' = W^{1 - R} \times W_{\text{avg}}^R$$
Where:
- $W'$ = New adjusted weight
- $W$ = Original base weight
- $W_\text{avg}$ = Average weight of all items
- $R$ = Rarity shift factor, increasing with IIR:
$$R = \frac{IIR}{100+IIR}$$

as $IIR \to \infty$, we get $R \to 1$, which simplifies the formula to:
$$W' = W_{\text{avg}}$$

This means that at **infinite IIR**, all weights become equal, making every item equally likely to drop.

### Increased Item Rarity (IIR) for Equipment Drops

Players can have **Increased Item Rarity (IIR)** on their equipment, which improves the chances of dropped items having pre-existing modifiers.

By default:

- Items have a **20% chance** to upgrade to a **Magic** item.
- Items have a **5% chance** to upgrade to a **Rare** item.

**IIR scales these chances linearly.**

#### Drop Process:

1. **Rare Item Check:**
    - If successful, the item starts with **3-5 modifiers**.
    - A secondary roll is made using the **base rare chance × IIR**.
    - If this roll succeeds, an extra modifier is added.
    - This process repeats up to until the item have **6 modifiers**.
2. **Magic Item Check (if Rare failed):**
    - If successful, the item gains **1-2 modifiers**.
3. **Common Item (if both fail):**
    - The item remains **unmodified**.
4. **If the item is rare or magic**:
	- We start a roll chain with a base chance to stop, the base chance uses the formula:
	$$Base = \text{Max}\left(10, 100 \times \left(1 - e ^{-0.02 \times \frac{IIR}{10}}\right)\right) $$
	 At 0% IIR → $Base = 10\%$ (90% chance to stop the chain)
	 At 100% IIR → $Base \approx 18.1\%$ (81.9% chance to stop the chain)
    At 500% IIR → $Base \approx 63.2\%$ (36.8% chance to stop the chain)
	- For each successful roll, *all* modifiers' tier will be upgraded to the next tier, these upgrades bypasses any item level requirement.

>_Due to client/server limitations, rolled modifiers do not appear in the loot window.
Hovering an item in the loot window will display a <span style="color:#8788EE">\<Item may have additional modifiers></span> tooltip._

