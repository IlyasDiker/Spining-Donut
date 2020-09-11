# Spining-Donut
A simple spinning Donut using MATH

<div class="post p2 p-responsive wrap" role="main">
      <div class="measure">
        <div class="post-header mb2">
  <h1>Donut math: how donut.c works</h1>
  <span class="post-meta">Jul 20, 2011</span><br>
  
</div>

<article class="post-content">
  <p>There has been a sudden resurgence of interest in my <a href="/2006/09/15/obfuscated-c-donut.html">“donut” code from 2006</a>, and I’ve
had a couple requests to explain this one.  It’s been five years now, so it’s
not exactly fresh in my memory, so I will reconstruct it from scratch, in great
detail, and hopefully get approximately the same result.</p>

<p>This is the code:</p>
<pre style="font-size: 10pt;">             k;double sin()
         ,cos();main(){float A=
       0,B=0,i,j,z[1760];char b[
     1760];printf("\x1b[2J");for(;;
  ){memset(b,32,1760);memset(z,0,7040)
  ;for(j=0;6.28&gt;j;j+=0.07)for(i=0;6.28
 &gt;i;i+=0.02){float c=sin(i),d=cos(j),e=
 sin(A),f=sin(j),g=cos(A),h=d+2,D=1/(c*
 h*e+f*g+5),l=cos      (i),m=cos(B),n=s\
in(B),t=c*h*g-f*        e;int x=40+30*D*
(l*h*m-t*n),y=            12+15*D*(l*h*n
+t*m),o=x+80*y,          N=8*((f*e-c*d*g
 )*m-c*d*e-f*g-l        *d*n);if(22&gt;y&amp;&amp;
 y&gt;0&amp;&amp;x&gt;0&amp;&amp;80&gt;x&amp;&amp;D&gt;z[o]){z[o]=D;;;b[o]=
 ".,-~:;=!*#$@"[N&gt;0?N:0];}}/*#****!!-*/
  printf("\x1b[H");for(k=0;1761&gt;k;k++)
   putchar(k%80?b[k]:10);A+=0.04;B+=
     0.02;}}/*****####*******!!=;:~
       ~::==!!!**********!!!==::-
         .,~~;;;========;;;:~-.
             ..,--------,*/
</pre>

<p>At its core, it’s a framebuffer and a Z-buffer into which I render pixels.
Since it’s just rendering relatively low-resolution ASCII art, I massively
cheat.  All it does is plot pixels along the surface of the torus at
fixed-angle increments, and does it densely enough that the final result looks
solid.  The “pixels” it plots are ASCII characters corresponding to the
illumination value of the surface at each point: <code class="highlighter-rouge">.,-~:;=!*#$@</code> from dimmest to
brightest.  No raytracing required.</p>

<p>So how do we do that?  Well, let’s start with the basic math behind 3D
perspective rendering.  The following diagram is a side view of a person
sitting in front of a screen, viewing a 3D object behind it.</p>

<center><img src="https://www.a1k0n.net/img/perspective.png"></center>

<p>To render a 3D object onto a 2D screen, we project each point (<em>x</em>,<em>y</em>,<em>z</em>) in
3D-space onto a plane located <em>z’</em> units away from the viewer, so that the
corresponding 2D position is (<em>x’</em>,<em>y’</em>).  Since we’re looking from the side,
we can only see the <em>y</em> and <em>z</em> axes, but the math works the same for the <em>x</em>
axis (just pretend this is a top view instead).  This projection is really easy
to obtain: notice that the origin, the <em>y</em>-axis, and point (<em>x</em>,<em>y</em>,<em>z</em>) form a
right triangle, and a similar right triangle is formed with (<em>x’</em>,<em>y’</em>,<em>z’</em>).
Thus the relative proportions are maintained:</p>

<img src="https://i.imgur.com/C8pPwyr.png">
