# Excluded: gradient and Jacobian methods

Listed so they are not rediscovered and proposed again. They overlap the J-space (Jacobian-lens) line of work, which is the gradient side by design.

| Method | Why excluded | Reference |
|---|---|---|
| Jacobian SAEs | sparsifies the Jacobian; direct overlap with J-space | arXiv 2502.18147 |
| Attribution patching / AtP* | gradient approximation to activation patching (MI-11) | arXiv 2403.00745 |
| Integrated gradients, saliency, input x gradient | input-space gradient attribution | standard |
| MI-04 APD | its attribution step is gradient-based | see methods page |
| MI-05 SPD | gradient-trained components | see methods page |
| MI-12 RelP | a backward pass with respect to the input, even though the rules are not gradients | see methods page |

Rule of thumb: "attribution" is not automatically gradient-based. Check each step; if it is a backward pass with respect to the input, it is out.
