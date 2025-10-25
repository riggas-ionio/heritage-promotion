---
layout: page
title: Δεδομένα από Wikidata
permalink: /wikidata-content/
---

# 📜 Ανακτήσεις Δηλώσεων από Wikidata (Α' Π.Π. - Q361)

Το παρακάτω περιεχόμενο είναι αποτέλεσμα δυναμικής ανάκτησης **Δηλώσεων (Claims)** από το Wikidata.

<div id="wikidata-claims-content" style="border: 1px solid #005A9C; padding: 20px; background-color: #E6F3FF; margin-top: 20px; border-radius: 5px;">
  Φόρτωση δεδομένων και Δηλώσεων...
</div>

<script>
// QID: World War I (Q361) - Περισσότερες δηλώσεις για επίδειξη
const QID_DEMO = 'Q361'; 
const WIKIDATA_API_URL = `https://www.wikidata.org/w/api.php?action=wbgetentities&ids=${QID_DEMO}&props=labels|descriptions|claims&languages=el|en&format=json&origin=*`;

const contentDiv = document.getElementById('wikidata-claims-content');

fetch(WIKIDATA_API_URL)
  .then(response => response.json())
  .then(data => {
    const entity = data.entities[QID_DEMO];

    if (!entity || entity.missing === "") {
        contentDiv.innerHTML = `<h3>Δεν βρέθηκαν δεδομένα για το QID: ${QID_DEMO}</h3>`;
        return;
    }

    // 1. Βασικές πληροφορίες
    const label_el = entity.labels.el ? entity.labels.el.value : entity.labels.en.value;
    const description_el = entity.descriptions.el ? entity.descriptions.el.value : entity.descriptions.en.value;

    let html = `<h2>${label_el}</h2>`;
    html += `<p><strong>Περιγραφή:</strong> ${description_el}</p>`;
    html += `<p><strong>Link στο Wikidata:</strong> <a href="https://www.wikidata.org/wiki/${QID_DEMO}">QID: ${QID_DEMO}</a></p><hr>`;
    
    // 2. Εμφάνιση συγκεκριμένων ΔΗΛΩΣΕΩΝ (Claims)

    html += '<h3>Επιλεγμένες Δηλώσεις:</h3><ul>';
    
    // P580: Ημερομηνία έναρξης (start time)
    if (entity.claims && entity.claims.P580) {
      // Λαμβάνουμε την τιμή (value) της πρώτης δήλωσης
      const startTime = entity.claims.P580[0].mainsnak.datavalue.value.time;
      html += `<li><strong>Ημερομηνία Έναρξης (P580):</strong> ${startTime.substring(1, 11)}</li>`; // Αφαίρεση του '+' και ώρας
    }

    // P710: Συμμετέχοντες (participants) - Εμφάνιση του πλήθους
    if (entity.claims && entity.claims.P710) {
      const participantsCount = entity.claims.P710.length;
      html += `<li><strong>Πλήθος Συμμετεχόντων (P710):</strong> ${participantsCount} καταχωρήσεις</li>`;
    }
    
    // P279: Υποκατηγορία του (subclass of)
    if (entity.claims && entity.claims.P279) {
      html += `<li><strong>Υποκατηγορία του (P279):</strong> Διαθέσιμες ${entity.claims.P279.length} δηλώσεις (βλέπε JSON)</li>`;
    }

    html += '</ul>';

    contentDiv.innerHTML = html;

  })
  .catch(error => {
    console.error('Σφάλμα φόρτωσης Wikidata:', error);
    contentDiv.innerHTML = 'Σφάλμα φόρτωσης δεδομένων από το Wikidata API.';
  });
</script>
