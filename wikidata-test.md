---
layout: page
title: Δοκιμή Wikidata
permalink: /wikidata-test/
---

# 🔎 Δεδομένα από το Wikidata (Q136545857)

Το περιεχόμενο που ακολουθεί ανακτάται απευθείας από το Wikidata χρησιμοποιώντας JavaScript.

<div id="wikidata-content" style="border: 1px solid #ccc; padding: 15px; background-color: #f9f9f9;">
  Φόρτωση δεδομένων...
</div>

<script>
// Το QID του αντικειμένου που θέλουμε να ανακτήσουμε
const QID = 'Q136545857';

// Διεύθυνση API του Wikidata (έχει CORS enabled)
const WIKIDATA_API_URL = `https://www.wikidata.org/w/api.php?action=wbgetentities&ids=${QID}&props=labels|descriptions&languages=el|en&format=json&origin=*`;

const contentDiv = document.getElementById('wikidata-content');

fetch(WIKIDATA_API_URL)
  .then(response => response.json())
  .then(data => {
    // Έλεγχος αν υπάρχουν δεδομένα για το QID
    if (data.entities && data.entities[QID]) {
      const entity = data.entities[QID];

      // Ανάκτηση τίτλου (Label)
      const label_el = entity.labels && entity.labels.el ? entity.labels.el.value : entity.labels.en ? entity.labels.en.value : 'Χωρίς Τίτλο (Label)';
      
      // Ανάκτηση Περιγραφής (Description)
      const description_el = entity.descriptions && entity.descriptions.el ? entity.descriptions.el.value : entity.descriptions.en ? entity.descriptions.en.value : 'Δεν βρέθηκε περιγραφή.';

      // Δημιουργία HTML περιεχομένου
      contentDiv.innerHTML = `
        <h3>Τίτλος (Ελληνικά): ${label_el}</h3>
        <p><strong>QID:</strong> ${QID}</p>
        <p><strong>Περιγραφή:</strong> ${description_el}</p>
      `;
    } else {
      contentDiv.innerHTML = 'Δεν βρέθηκαν δεδομένα για αυτό το QID.';
    }
  })
  .catch(error => {
    console.error('Σφάλμα φόρτωσης Wikidata:', error);
    contentDiv.innerHTML = 'Σφάλμα φόρτωσης δεδομένων από το Wikidata API.';
  });
</script>
