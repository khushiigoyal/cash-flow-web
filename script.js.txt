document.getElementById('flowForm').addEventListener('submit', function (e) {
  e.preventDefault();

  const input = document.getElementById('input').value.trim();
  const lines = input.split('\n');
  const n = 100;
  const net = Array(n).fill(0);

  for (let line of lines) {
    let [u, v, w] = line.trim().split(' ').map(Number);
    if (!isNaN(u) && !isNaN(v) && !isNaN(w)) {
      net[u] -= w;
      net[v] += w;
    }
  }

  const output = [];
  function getMaxCredit() {
    let maxIndex = 0;
    for (let i = 1; i < n; i++) {
      if (net[i] > net[maxIndex]) maxIndex = i;
    }
    return maxIndex;
  }

  function getMaxDebit() {
    let minIndex = 0;
    for (let i = 1; i < n; i++) {
      if (net[i] < net[minIndex]) minIndex = i;
    }
    return minIndex;
  }

  function minCashFlow() {
    let mxCredit = getMaxCredit();
    let mxDebit = getMaxDebit();

    if (net[mxCredit] === 0 && net[mxDebit] === 0) return;

    let min = Math.min(-net[mxDebit], net[mxCredit]);
    net[mxCredit] -= min;
    net[mxDebit] += min;

    output.push(`Person ${mxDebit} pays ${min} to Person ${mxCredit}`);
    minCashFlow();
  }

  minCashFlow();

  const outList = document.getElementById('output');
  outList.innerHTML = '';
  if (output.length === 0) {
    outList.innerHTML = '<li>No transactions to minimize.</li>';
  } else {
    output.forEach((line) => {
      const li = document.createElement('li');
      li.textContent = line;
      outList.appendChild(li);
    });
  }
});
