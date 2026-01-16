---
date: '2026-01-16T22:23:32Z'
draft: false
title: 'Triton_note'
---
### grid = n_elements / block_size
n_elements：总共要处理的元素总数（运行时参数）
block_size：每一份的 tile 大小（每个 program 处理多少数据）编译期常量，tl.constexpr）
grid：总共要开多少个program（切成多少份）