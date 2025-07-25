# 📐 Geometric Formulas Table | جدول القوانين الهندسية

| English Name           | الاسم بالعربية        | Perimeter (P)                             | Area (A)                                           | Volume (V)                                  |
|------------------------|------------------------|-------------------------------------------|----------------------------------------------------|---------------------------------------------|
| **Square**             | مربع                   | P = 4 × a                                 | A = a²                                             | —                                           |
| **Rectangle**          | مستطيل                 | P = 2 × (l + w)                           | A = l × w                                          | —                                           |
| **Triangle**           | مثلث                   | P = a + b + c                             | A = ½ × base × height                              | —                                           |
| **Equilateral Triangle** | مثلث متساوي الأضلاع | P = 3a                                   | A = (√3 / 4) × a²                                  | —                                           |
| **Circle**             | دائرة                  | P = 2πr                                   | A = πr²                                            | —                                           |
| **Parallelogram**      | متوازي أضلاع           | P = 2 × (a + b)                           | A = base × height                                  | —                                           |
| **Trapezoid**          | شبه منحرف              | P = a + b + c + d                         | A = ½ × (a + b) × height                           | —                                           |
| **Regular Polygon**    | مضلع منتظم             | P = n × a                                 | A = (n × a²) / (4 × tan(π/n))                      | —                                           |
| **Cube**               | مكعب                   | P = 12 × a                                | A = 6a²                                            | V = a³                                      |
| **Rectangular Prism**  | متوازي مستطيلات        | —                                         | A = 2(lw + lh + wh)                                | V = l × w × h                               |
| **Triangular Prism**   | منشور ثلاثي            | —                                         | A = bh + (a + b + c) × H                           | V = ½ × b × h × H                           |
| **Cylinder**           | أسطوانة                | —                                         | A = 2πr(h + r)                                     | V = πr²h                                    |
| **Cone**               | مخروط                  | —                                         | A = πr(l + r)                                      | V = (1/3)πr²h                               |
| **Sphere**             | كرة                    | —                                         | A = 4πr²                                           | V = (4/3)πr³                                |
| **Hemisphere**         | نصف كرة                | —                                         | A = 3πr²                                           | V = (2/3)πr³                                |
| **Square Pyramid**     | هرم رباعي منتظم        | —                                         | A = a² + 2a × slant_height                         | V = (1/3) × a² × h                          |
| **Tetrahedron**        | رباعي وجوه منتظم       | —                                         | A = √3 × a²                                        | V = (a³) / (6√2)                            |

---

## 🔑 Legend | الرموز:
- `a` = طول الضلع  
- `l` = الطول  
- `w` = العرض  
- `h` = الارتفاع الرأسي  
- `H` = ارتفاع المنشور  
- `b` = القاعدة  
- `r` = نصف القطر  
- `n` = عدد الأضلاع  
- `π` ≈ 3.14159  
- `l` (in cone) = الارتفاع الجانبي (المائل)  
- `bh` = القاعدة × الارتفاع في المثلث  
- `slant_height` = الارتفاع الجانبي للهرم  



### area of triangle using 3 sides
```
s=(a+b+c)/2
area=sqrt(s*(s-a)*(s-b)*(s-c));
```

### angle c in triangle  in degrees
```
ld angle=(acos((a*a+b*b-c*c)/(2*a*b)))*180/PI;
```


