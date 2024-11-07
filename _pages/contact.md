---
layout: page
title: Contact
permalink: /contact/
nav: false
nav_order: 0
social: true
---

<div>
  <!-- Contact Information -->
  <div>
    <p>Feel free to reach out for collaborations or inquiries!<br></p>
    
    <p>Email: <a href="mailto:fp409@cam.ac.uk">fp409@cam.ac.uk</a><br>
    Network:  
        {% if page.social %}
          <span class="social" style="display: inline-block; text-align: left; margin: 0; padding: 0;">
            <span class="contact-icons" style="display: inline-block; transform: scale(0.5); transform-origin: left; vertical-align: middle; margin: 0; padding: 0;">
              {% include social.liquid %}
            </span>
          </span>
        {% endif %}<br>    
    Address:<br>
      Department of Pathology<br>
      University of Cambridge<br>
      Tennis Court Road<br>
      Cambridge<br>
      CB2 1QP<br>
      United Kingdom
    </p>

  </div>

  <!-- Map -->
  <div style="margin-top: 20px;">
    <iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d19568.3389553231!2d0.11103926328307534!3d52.1881025564418!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x47d871d9b372538b%3A0x2eccbca777dde7ce!2sDepartment%20of%20Pathology!5e0!3m2!1spt-PT!2suk!4v1730511707317!5m2!1spt-PT!2suk" 
            width="100%" height="300" style="border:0;" allowfullscreen="" loading="lazy" referrerpolicy="no-referrer-when-downgrade"></iframe>
  </div>
</div>


