# AECS as a Biochemical Regulatory Cell (Reaction–ODE Form)

## 1) Molecular species (concentrations)

* $A$ = learning-rate kinase (active LR)
* $M$ = momentum polymer (inertial memory)
* $W$ = decay ligase (weight decay)
* $\Sigma$ = dither/noise transducer
* $G$ = gradient signal (nutrient/stress, externally sensed)
* $\widehat{G}$ = EMA observer of gradient (slow sensor)
* $V$ = velocity filaments (momentum buffer)
* $E$ = energy / effective step demand (target $\gamma^\*$)

State vector: $\;x=\big[\theta,\;A,\;M,\;W,\;\Sigma,\;\widehat{G},\;V\big]$.

## 2) Sensing & transduction (fast reactions)

**Gradient sensing**

* $G \xrightarrow{k_s} \widehat{G}$, with leakage $\widehat{G}\xrightarrow{k_\ell}\varnothing$.

  $$
  \dot{\widehat{G}}=k_s(G-\widehat{G})-k_\ell\widehat{G}
  $$

**Effective-step error (resonance lock)**

* Error: $e = E - A\,\|G\|$ (target $E=\gamma^\*$).
* LR kinase activation/inhibition:

  $$
  \varnothing \xrightleftharpoons[k_{A^-}H_-(e)]{k_{A^+}H_+(e)} A
  $$

  with smooth Hill gates $H_+(e)=\frac{e_+^n}{e_+^n+K_A^n}$, $H_-(e)=\frac{(-e)_+^n}{(-e)_+^n+K_A^n}$.

## 3) Homeostatic core (slow controllers)

**Momentum plasticity (stability ↔ plasticity)**

* When stable ($\|G\|<\widehat{G}$): polymerize $M$.
* When noisy ($\|G\|>\widehat{G}$): depolymerize $M$.

  $$
  \dot M = k_{M^+}\,\sigma(\widehat{G}-\|G\|)\,(1-M)-k_{M^-}\,\sigma(\|G\|-\widehat{G})\,M
  $$

  where $\sigma(z)=\frac{1}{1+e^{-z/T}}$.

**Adaptive decay**

$$
\dot W = k_W\big(\|G\|-\widehat{G}\big)-\lambda_W W \quad\;\;(\text{clamped to }[0,W_{\max}])
$$

**Dither transducer (exploration fuel)**

$$
\dot\Sigma = -\lambda_\Sigma \Sigma + k_\Sigma\,\sigma(\text{plateau}) \quad\text{with plateau} := \operatorname{Var}_\tau(\mathcal{L})<\epsilon
$$

## 4) Motion apparatus (parameter actin–myosin)

**Velocity filaments**

$$
\dot V = -\lambda_V V + G
$$

**Parameter transport (plant)**

$$
\dot\theta = -A\,G + M\,V + \Sigma\,\xi(t)
$$

with $\xi(t)$ zero-mean reparameterized noise (e.g., $\xi=\epsilon$, $\epsilon\sim\mathcal N(0,I)$).

## 5) Smooth “burst” as graded biochemical switch (no hard thresholds)

Let stagnation signal $S=\sigma\!\big(\epsilon-\operatorname{Var}_\tau(\mathcal{L})\big)\in[0,1]$.

$$
\dot A \;+=\; k_b\,S\,(A_b-A), \qquad
\dot \Sigma \;+=\; k_{b\Sigma}\,S\,(\Sigma_b-\Sigma), \qquad
\dot M \;-\!=\; k_{bM}\,S\,M
$$

(graded burst toward targets $A_b>\!A$, $\Sigma_b>\!\Sigma$, with momentum damped).

## 6) Compact ODE system (clamped, all smooth)

$$
\begin{aligned}
\dot{\widehat{G}}&=k_s(G-\widehat{G})-k_\ell\widehat{G} \\
\dot A&=\alpha_A\big[H_+(E-A\|G\|)-H_- (E-A\|G\|)\big] + k_b S(A_b-A) \\
\dot M&=k_{M^+}\sigma(\widehat{G}-\|G\|)(1-M)-k_{M^-}\sigma(\|G\|-\widehat{G})M - k_{bM} S M \\
\dot W&=k_W(\|G\|-\widehat{G})-\lambda_W W \\
\dot \Sigma&=-\lambda_\Sigma\Sigma + k_\Sigma\sigma(\text{plateau}) + k_{b\Sigma}S(\Sigma_b-\Sigma) \\
\dot V&=-\lambda_V V + G \\
\dot\theta&=-A\,G + M\,V + \Sigma\,\xi(t)
\end{aligned}
$$

All states are bounded to biophysical ranges: $A\!\in[A_{\min},A_{\max}], M\!\in[0,1], W\!\in[0,W_{\max}], \Sigma\!\in[0,\Sigma_{\max}]$.

## 7) Energetics & Lyapunov–homeostasis

Choose potential $V_L(\theta)=\mathcal L(\theta)$ (or $\frac12\|\theta-\theta^\*\|^2$). In expectation over $\xi$,

$$
\mathbb E[\dot V_L] \approx -A\,\|G\|^2 + M\,\langle V,G\rangle + \tfrac12\Sigma^2 \operatorname{Tr}(\nabla^2 V_L)
$$

The **resonance lock** $A\|G\|\!\approx\!E$, **momentum damping** when noisy, and **bounded $\Sigma$** ensure $\mathbb E[\dot V_L]\!<\!0$ away from flat minima, yielding stochastic Lyapunov stability (homeostasis). “Bursts” are *graded* (via $S$) so stability is preserved while enabling valley escapes.

## 8) Differentiable organism (meta-trainable)

* All gates use smooth sigmoids/Hill functions → system is differentiable end-to-end.
* Reparameterized noise ($\xi=\epsilon$) allows gradients through exploration.
* Controller gains $\Theta_c=\{k_\cdot,\lambda_\cdot,E,\text{targets}\}$ are **meta-parameters** learnable by outer gradients or RL.

## 9) Interpretive map (bio ↔ optimization)

* $A$: metabolic throttle (learning rate).
* $M$: cytoskeletal memory (momentum).
* $W$: autophagy/ubiquitin ligase (weight decay).
* $\Sigma$: heat-shock/chemotaxis noise (exploration).
* $G,\widehat{G}$: receptors + second messengers (signal & observer).
* $S$: quorum-sensed plateau hormone (graded burst).

**AECS-as-cell**: a self-regulating, differentiable biochemical network that maintains **homeostasis (convergence)** while retaining **plasticity (exploration)** through smooth, controllable reaction kinetics.
