---
title: Computer Vision
---

## 3.1 에지 강도와 방향


∇f(y,x)=(∂f/∂x,∂f/∂y)=(d_y,d_x )=(f(y+1),x)−f(y−1,x),f(y,x+1)−f(y,x−1))          (3.5)

이에 해당하는 마스크 : m_y= ■8(−1@0@1), m_x= ■8(−1&0&1)

그레이디언트 : ∇f=(∂f/∂x,∂f/∂y)=(d_y,d_x )
 에지 강도 : S(y,x)=magnitude(∇f)=√(〖d_y〗^2+〖d_x〗^2 )       (3.6)
그레이디언트 방향 : D(y,x)=arctan⁡(d_y/d_x )
