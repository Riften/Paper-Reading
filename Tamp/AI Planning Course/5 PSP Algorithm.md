# PSP Algorithm
Plan-Space Planning Algorithm。

算法的基本想法就是前面说过的，在保持 $\prec$ 和 $B$ 的合法性前提下减少 flaw。基本流程如下
- 找到当前 $\pi$ 中的 flaw
- 选择一个 flaw
- 找到解决这个 flaw 的方法，该方法被称为 resolver。resolver 必须保持 $\prec$ 和 $B$ 的合理性。
- 选择一个 resolver
- 根据选定的 resolver 优化 $\pi$

需要注意的是，对 resolver 和 flaw 的选择策略是不同的
- $resolver\leftarrow allResolvers.\text{chooseOne}$
  - non-deterministic choice
  - 如果一个不行，那么就剪枝并且尝试下一个
- $flaw \leftarrow allFlaws.\text{selectOne}$
  - deterministic selection
  - flaw 都需要去掉，去掉的顺序不影响正确性，但影响算法效率
  - 不会回过头来解决无法满足的 flaw。

## Implementation
### $plan.\text{openGoals}()$
获取当前 partial plan 中还没有达成的 sub-goals。以 incrementally 的方式实现
- 初始时为所有 goal conditions
- adding action 的时候，添加所有 preconditions
- adding causal link 的时候，移除所有被 proposition 维护的 sub-goal

### $plan.\text{threats}()$
获取当前所有 threats，同样是 incrementally 实现
- 初始时没有 threats
- adding action 的时候：检查当前每一个 causal link $\langle a_i - [p] \rightarrow a_j \rangle$
  - 如果 $(a_{new} \prec a_i)$ 或者 $(a_j \prec a_{new})$，那就检查下一个 causal link
  - 否则，对于 $a_{new}$ 的每一个 effect $q$
    - 如果 $(\exist \sigma: \sigma(p) = \sigma(\neg q))$ 那么 $q$ 就是该 causal link 的一个 threat。也就是说有 variable 取值可以让 $p$ 和 $\neg q$ 相同。
- adding causal link 的时候
  - 对已有的所有 action 用上面的方法判断 threaten。需要注意的是，作为 provider 和 consumer 的 action，只有 provider 需要检查。

### $flaw.\text{getResolvers}(plan)$