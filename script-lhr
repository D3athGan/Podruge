<script>
const url = 'https://raw.githubusercontent.com/D3athGan/Podruge/refs/heads/main/test_catalog.json';
const clinicAddress = "Кутузовский пр., д. 34, стр. 14";
// const clinicAddress = localStorage.getItem("selectedClinicAddress");
console.log("Выбранная клиника:", clinicAddress);

let currentFilters = {
  gender: 'women',
  visitType: 'prices',
  priceType: 'standard'
};

function getFilterValues() {
  return currentFilters;
}

function pluralizeVisits(count) {
  if (count < 5) return `${count} посещения`;
  return `${count} посещений`;
}

function formatPrice(value) {
  return value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ');
}

function renderCard(service, gender, visitType, priceType) {
  const name = service.name;
  const desc = service.description || "";
  const isSelected = service.select === true;

  let pricesHTML = '';

  if (visitType === 'prices') {
    const genderPrices = service.prices && service.prices[gender];
    if (!genderPrices) return '';
    const standard = priceType === 'standard' ? genderPrices.standard_price : genderPrices.top_price;
    const club = priceType === 'standard' ? genderPrices.club_card_price : genderPrices.top_card_price;
    if (standard == null || club == null) return '';

    pricesHTML += `
      <span><span class="price-label">с картой:</span><span class="price-value">${formatPrice(club)} руб</span></span>
      <span><span class="price-label">без карты:</span><span class="price-value">${formatPrice(standard)} руб</span></span>
    `;
  } else if (visitType === 'subscriptions') {
    const subs = service.subscriptions || {};
    const subBlock = priceType === 'standard' ? subs.regular : subs.top;
    if (!subBlock || typeof subBlock !== 'object') return '';

    Object.keys(subBlock).forEach(key => {
      const match = key.match(/(\d+)_sessions/);
      if (!match) return;
      const count = parseInt(match[1]);
      const price = subBlock[key];
      if (price == null) return;
      pricesHTML += `<span><span class="visits-label">${pluralizeVisits(count)}:</span><span class="price-value">${formatPrice(price)} руб</span></span>`;
    });
  }

  return {
    html: `
      <div class="card${isSelected ? ' selected' : ''}">
        <div class="info">
          <div class="label">${name}</div>
          ${desc ? `<div class="desc">${desc}</div>` : ''}
        </div>
        <div class="prices">
          ${pricesHTML}
        </div>
      </div>
    `,
    isSelected
  };
}

function updateData() {
  const { gender, visitType, priceType } = getFilterValues();

  fetch(url)
    .then(res => res.json())
    .then(data => {
      const diodeBlock = document.getElementById('diode');
      const alexBlock = document.getElementById('alex');

      diodeBlock.innerHTML = '';
      alexBlock.innerHTML = '';

      const records = data.records || [];

      records.forEach(record => {
          if (record.category !== 'Лазерная эпиляция') return;
          if (!Array.isArray(record.clinics) || !record.clinics.some(c => c.address === clinicAddress)) return;
        
          const services = record.services || [];
        
          const rendered = services
            .map(service => renderCard(service, gender, visitType, priceType))
            .filter(card => card);
        
          const selectedCards = rendered.filter(c => c.isSelected).map(c => c.html).join('');
          const normalCards = rendered.filter(c => !c.isSelected).map(c => c.html).join('');
          const combinedHTML = selectedCards + normalCards;
        
          if (record.subcategory === 'Диодный лазер' && record.equip === 'Diode') {
            diodeBlock.innerHTML += combinedHTML;
          }
          if (record.subcategory === 'Александритовый лазер' && record.equip === 'Alex') {
            alexBlock.innerHTML += combinedHTML;
          }
        });
    });
}

document.querySelectorAll('.tab-button').forEach(button => {
  button.addEventListener('click', () => {
    const group = button.parentElement;
    group.querySelectorAll('.tab-button').forEach(btn => btn.classList.remove('active'));
    button.classList.add('active');

    const filter = button.dataset.filter;
    const value = button.dataset.value;
    currentFilters[filter] = value;

    updateData();
  });
});

document.querySelectorAll('.tab-button[data-filter]').forEach(btn => {
  if (btn.dataset.value === currentFilters[btn.dataset.filter]) {
    btn.classList.add('active');
  }
});

updateData();
setInterval(updateData, 1000);
</script>
