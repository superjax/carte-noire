---
layout: post
title:  "Rotations CheatSheet"
date:   2017-12-16 20:17:00 -0600
categories: Math, Rotations, Quaternions, Derivatives, Robotics, Equations of Motion
---

There are number of identities that I commonly need when dealing with rotations. This post is designed to briefly talk about a lot of the common difficulties that I have with rotations, and as a place for me to record these ideas for quick review later.

I'm going to use the following notation for describing coordinate frames:

* \\( q_i^b \quad \\) - Quaternion describing rotation from the inertial frame to the body frame
* \\( w_{b/i}^{b} \quad \\) - Angular velocity of the body frame, with respect to the inertial frame, expressed in the body frame


# Skew-Symmetric Matrix

I'm going to make extensive use of the skew-symmetric matrix, defined as

$$
\left\lfloor v\right\rfloor =\left[\begin{array}{ccc}
0 & -v_{3} & v_{2}\\
v_{3} & 0 & -v_{1}\\
-v_{2} & v_{1} & 0
\end{array}\right]
$$


which is related to taking cross-product between two vectors, that
is
$$
v\times w=\left\lfloor v\right\rfloor w
$$


The skew-symmetric matrix has the following handy identities

$$
\begin{aligned}\left\lfloor u\right\rfloor v & =-\left\lfloor v\right\rfloor u\end{aligned}
$$


$$
\begin{aligned}\left\lfloor v\right\rfloor v & =0\end{aligned}
$$


And, if $R$ is a rotation matrix, (which we will talk about later)

$$
\left\lfloor Rv\right\rfloor =R\left\lfloor v\right\rfloor R^{\top}
$$



# Rotation Conventions


## Quaternions

A unit Quaternion is a 4-dimensional object that extevelocitynds the
complex numbers and represents a rotation in \\( SO\left(3\right) \\).
We will use quaternions extensively, and all rotations will be based
around the unit quaternion representation. I'm going to use Hamiltonian
(right-handed) quaternion and will be expressed as column vectors
as shown below.

$$
q=q_{w}+q_{x}i+q_{y}j+q_{z}k=\begin{bmatrix}q_{w}\\
\bar{q}
\end{bmatrix}
$$

The use of Hamiltonian quaternions result in multiplication being
defined as

$$
p\otimes q=\begin{bmatrix}p_{w} & -\bar{p}^{\top}\\
\bar{p} & p_{w}I_{3\times3}+\left\lfloor \bar{p}\right\rfloor
\end{bmatrix}\begin{bmatrix}q_{w}\\
\bar{q}
\end{bmatrix}
$$


A quaternion times its inverse is the identity quaternion

$$
q\otimes q^{-1}=\begin{bmatrix}1\\
0
\end{bmatrix}
$$


and a quaternion \\( q \\) can be used to rotate a vector \\( u \\) with

$$
u\mapsto q^{-1}\otimes\left[\begin{array}{c}
0\\
u
\end{array}\right]\otimes q
$$


and the concatenation of quaternions is straight-forward

$$
q_{1}^{n}=q_{1}^{2}\otimes q_{2}^{3}\cdots q_{n-1}^{n}
$$


Given two coordinate frames, described by \\( q_{i}^{a} \\) and \\( q_{i}^{b} \\)
referenced to the same coordinate frame, we can determine the quaternion
describing the rotation \\( q_{a}^{b} \\) with

$$
\begin{aligned}q_{a}^{b} & =q_{a}^{i}\otimes q_{i}^{b}\\
q_{a}^{b} & =q_{i}^{a,-1}\otimes q_{i}^{b}
\end{aligned}
$$



## Rotation Matrix

Most commonly, we will express rotations in equations as a rotation
matrix, based on a quaternion. A rotation matrix is a member of \\( \mathbb{R}^{3\times3} \\)
and describes a rotation in \\( SO\left(3\right) \\) Rotation matrices
have a determinant of 1 and all rows and columns are orthogonal.

The \\( 3\times3 \\) Rotation matrix \\( R\left(q\right) \\) based on a quaternion
is defined as follows

$$
R\left(q\right)=\left(2q_{w}^{2}-1\right){\bf I}-2q_{w}\left\lfloor \bar{q}\right\rfloor +2\bar{q}\bar{q}^{\top}\in\mathbb{R}^{3\times3}
$$


A rotation matrix transpose is the inverse of a rotation matrix

$$
R\left(q_{a}^{b}\right)^{\top}=R\left(q_{b}^{a}\right)=R\left(q_{a}^{b}\right)^{-1}
$$


Which leads to the following identities

$$
\begin{aligned}R\left(q_{a}^{b}\right)R\left(q_{a}^{b}\right)^{\top} & =I\\
R\left(q_{a}^{b}\right)^{\top}R\left(q_{a}^{b}\right) & =I
\end{aligned}
$$



# Active vs Passive Rotations

We have to be explicit about what the \\( R\left(q\right)\\) notation
means. In this document, \\( R\left(q\right)v\\) will represent a passive
rotation of \\(v\\). That is, \\( R\left(q\right)v\\) will result in the
original vector \\(v\\), represented in a new coordinate frame described
by \\( R\left(q\right)\\). The active rotation \\( R\left(q\right)^{\top}v\\)
results in a new rotated object expressed in the original frame.


# Time Derivative of Rotations

The time derivative of a passive rotation matrix between two arbitrary
(possibly time varying) frames, where \\(\omega \\) is the instantaneous
angular velocity.

$$
\begin{aligned}\frac{d}{dt}R\left(q_{a\left(t\right)}^{b\left(t\right)}\right) & =\left\lfloor \omega_{b\left(t\right)/a\left(t\right)}^{b\left(t\right)}\right\rfloor R\left(q_{a\left(t\right)}^{b\left(t\right)}\right)\end{aligned}
$$


If the rotation is with respect to a fixed frame,

$$
\begin{aligned}\frac{d}{dt}R\left(q_{a}^{b\left(t\right)}\right) & =\left\lfloor \omega_{b\left(t\right)/a}^{b\left(t\right)}\right\rfloor R\left(q_{a}^{b\left(t\right)}\right)\end{aligned}
$$


On the other hand, if the rotation matrix is defined with respect
to a time-varying frame and describes the rotation to a fixed frame,
(such as in the case of body-fixed dynamics), we can do some manipulation
using the skew-symmetric matrix to get the angular velocity into a
more convenient frame.

$$
\begin{aligned}\frac{d}{dt}R\left(q_{b\left(t\right)}^{a}\right) & =\left\lfloor \omega_{a/b\left(t\right)}^{a}\right\rfloor R\left(q_{b\left(t\right)}^{a}\right)\\
 & =\left\lfloor R\left(q_{b\left(t\right)}^{a}\right)\omega_{a/b\left(t\right)}^{b\left(t\right)}\right\rfloor R\left(q_{b\left(t\right)}^{a}\right)\\
 & =R\left(q_{b\left(t\right)}^{a}\right)\left\lfloor \omega_{a/b\left(t\right)}^{b\left(t\right)}\right\rfloor R\left(q_{b\left(t\right)}^{a}\right)^{\top}R\left(q_{b\left(t\right)}^{a}\right)\\
 & =R\left(q_{b\left(t\right)}^{a}\right)\left\lfloor \omega_{a/b\left(t\right)}^{b\left(t\right)}\right\rfloor \\
 & =-R\left(q_{b\left(t\right)}^{a}\right)\left\lfloor \omega_{b\left(t\right)/a}^{b\left(t\right)}\right\rfloor
\end{aligned}
$$


To confirm, we can take the time derivative of the fundamental identity
of rotation matrices and use the chain rule.

$$
\begin{eqnarray*}
R\left(q_{a}^{b\left(t\right)}\right)R\left(q_{a}^{b\left(t\right)}\right)^{\top} & = & I\\
\frac{d}{dt}\left(R\left(q_{a}^{b\left(t\right)}\right)R\left(q_{a}^{b\left(t\right)}\right)^{\top}\right) & = & 0\\
\frac{d}{dt}\left(R\left(q_{a}^{b\left(t\right)}\right)\right)R\left(q_{a}^{b\left(t\right)}\right)^{\top}+R\left(q_{a}^{b\left(t\right)}\right)\frac{d}{dt}\left(R\left(q_{a}^{b\left(t\right)}\right)^{\top}\right) & = & 0\\
\left\lfloor \omega_{b\left(t\right)/a}^{b\left(t\right)}\right\rfloor R\left(q_{a}^{b\left(t\right)}\right)R\left(q_{a}^{b\left(t\right)}\right)^{\top}-R\left(q_{a}^{b\left(t\right)}\right)R\left(q_{b\left(t\right)}^{a}\right)\left\lfloor \omega_{b\left(t\right)/a}^{b\left(t\right)}\right\rfloor  & = & 0\\
\left\lfloor \omega_{b\left(t\right)/a}^{b\left(t\right)}\right\rfloor -\left\lfloor \omega_{b\left(t\right)/a}^{b\left(t\right)}\right\rfloor  & = & 0
\end{eqnarray*}
$$


The active variants of these rotations are simply the transpose of
the passive versions, and therefore have flipped definition. However,
I'm going to re-derive them for completeness.

$$
\begin{eqnarray*}
\frac{d}{dt}R\left(q_{a\left(t\right)}^{b\left(t\right)}\right)^{\top} & = & \frac{d}{dt}R\left(q_{b\left(t\right)}^{a\left(t\right)}\right)
\end{eqnarray*}
$$

For fixed to rotating frames

$$
\begin{eqnarray*}
\frac{d}{dt}R\left(q_{a}^{b\left(t\right)}\right)^{\top} & = & \frac{d}{dt}R\left(q_{b\left(t\right)}^{a}\right)\\
 & = & -R\left(q_{b\left(t\right)}^{a}\right)\left\lfloor \omega_{b\left(t\right)/a}^{b\left(t\right)}\right\rfloor
\end{eqnarray*}
$$

and for rotating to fixed frames

$$
\begin{eqnarray*}
\frac{d}{dt}R\left(q_{b\left(t\right)}^{a}\right)^{\top} & = & \frac{d}{dt}R\left(q_{a}^{b\left(t\right)}\right)\\
 & = & \left\lfloor \omega_{b\left(t\right)/a}^{b\left(t\right)}\right\rfloor R\left(q_{a}^{b\left(t\right)}\right)
\end{eqnarray*}
$$

In this document, we will not discuss much the manifod nature of quaternions.
Quaternions do not form a vector space, so the derivative of a quaternion
is poorly defined in the quaternion group. The Lie algebra associated
with \\(SO\left(3\right)\\) is well-suited to defining this derivative,
but that is outside the scope of this work. For more information,
we direct the reader to [Hertzberg2013](https://arxiv.org/abs/1107.1119).


# Derivative of Commonly Used Expressions with Rotations

The derivative of a passively rotated vector, with respect to the
quaternion rotation. This happens all the time in 3D equations of
motion.

$$
\begin{aligned}\frac{d}{dq_{a}^{b}}\left(R\left(q_{a}^{b}\right)v\right) & =-\left\lfloor R\left(q_{a}^{b}\right)v\right\rfloor \end{aligned}
$$


and for the active rotation

$$
\frac{d}{dq_{a}^{b}}\left(R\left(q_{a}^{b}\right)^{\top}v\right)=\left\lfloor v\right\rfloor R\left(q_{a}^{b}\right)
$$


These expressions were found and checked with numerical differentiation.
Anyone who knows how to do this in a principled manner, please let
me know!
