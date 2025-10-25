---
layout: page
title: Πλήρες Περιεχόμενο Wikidata
permalink: /full-wikidata/
---

# 🌐 Πλήρης Ανάκτηση Δεδομένων από Wikidata

<p>Εμφάνιση όλων των Δηλώσεων (Claims) για το αντικείμενο QID: <strong>Q361 (Α' Παγκόσμιος Πόλεμος)</strong>.</p>

<div id="wikidata-full-content" style="border: 2px solid #CC0033; padding: 20px; background-color: #FEF0F0; border-radius: 8px;">
  Φόρτωση δεδομένων...
</div>

<script>
// QID: Α' Παγκόσμιος Πόλεμος
const QID = 'Q361'; 

// URL για ανάκτηση Labels, Descriptions και Claims
const WIKIDATA_API_URL = `https://www.wikidata.org/w/api.php?action=wbgetentities&ids=${QID}&props=labels|descriptions|claims&languages=el|en&format=json&origin=*`;

const contentDiv = document.getElementById('wikidata-full-content');

// Βοηθητική συνάρτηση για την εξαγωγή της τιμής από το JSON
function getClaimValue(claim) {
    if (!claim.mainsnak || !claim.mainsnak.datavalue) {
        return '— Άγνωστη Τιμή —';
    }
    const dataValue = claim.mainsnak.datavalue;
    const type = dataValue.type;

    switch (type) {
        case 'wikibase-entityid':
            // Για αντικείμενα (π.χ., QID), επιστρέφουμε τον ID
            return dataValue.value.id;
        case 'time':
            // Για ημερομηνίες, μορφοποιούμε
            return dataValue.value.time.substring(1, 11);
        case 'string':
        case 'external-id':
            return dataValue.value;
        case 'monolingualtext':
            return dataValue.value.text + ` (${dataValue.value.language})`;
        case 'quantity':
            return dataValue.value.amount + ' ' + (dataValue.value.unit === '1' ? '' : dataValue.value.unit);
        default:
            return `[Τύπος: ${type}]`;
    }
}

fetch(WIKIDATA_API_URL)
  .then(response => response.json())
  .then(data => {
    const entity = data.entities[QID];

    if (!entity || entity.missing === "") {
        contentDiv.innerHTML = `<h3>Δεν βρέθηκαν δεδομένα για το QID: ${QID}</h3>`;
        return;
    }

    // 1. Βασικές πληροφορίες
    const label_el = (entity.labels && entity.labels.el) ? entity.labels.el.value : (entity.labels && entity.labels.en) ? entity.labels.en.value : 'Χωρίς Τίτλο';
    const description_el = (entity.descriptions && entity.descriptions.el) ? entity.descriptions.el.value : (entity.descriptions && entity.descriptions.en) ? entity.descriptions.en.value : 'Χωρίς Περιγραφή';

    let html = `<h2>${label_el}</h2>`;
    html += `<p><strong>Περιγραφή:</strong> ${description_el}</p>`;
    html += `<p><a href="https://www.wikidata.org/wiki/${QID}" target="_blank">Προβολή στο Wikidata</a></p><hr>`;
    
    // 2. Πλήρης Λίστα Δηλώσεων (Claims)
    html += '<h3>Όλες οι Δηλώσεις (Claims):</h3><ul style="list-style-type: none; padding-left: 0;">';
    
    const claims = entity.claims;
    for (const propertyId in claims) {
      if (claims.hasOwnProperty(propertyId)) {
        const claimsList = claims[propertyId];
        
        claimsList.forEach(claim => {
            const value = getClaimValue(claim);
            // Εμφανίζουμε την ιδιότητα (P-number) και την ακατέργαστη τιμή (Value)
            html += `<li style="margin-bottom: 5px;">
                        <strong><a href="https://www.wikidata.org/wiki/${propertyId}" target="_blank">${propertyId}</a>:</strong> ${value}
                    </li>`;
        });
      }
    }

    html += '</ul>';

    contentDiv.innerHTML = html;

  })
  .catch(error => {
    console.error('Σφάλμα φόρτωσης Wikidata:', error);
    contentDiv.innerHTML = 'Σφάλμα φόρτωσης δεδομένων από το Wikidata API.';
  });
</script>
