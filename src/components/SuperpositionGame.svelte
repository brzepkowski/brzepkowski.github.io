<script>
  const instruments = [
    { name: 'Thermometer', letter: 'T', color: '#2a78d6' },
    { name: 'Hygrometer', letter: 'H', color: '#eb6834' },
    { name: 'Barometer', letter: 'B', color: '#2f9e44' },
    { name: 'Anemometer', letter: 'A', color: '#a855f7' },
  ];
  const ROUNDS = 5;
  const RES = 0.1;
  const cross = () => [
    { x: 1, y: 0 },
    { x: 0, y: 1 },
    { x: -1, y: 0 },
    { x: 0, y: -1 },
  ];
  const deg = (d) => (d * Math.PI) / 180;
  const rot = (a, d) => ({
    x: a.x * Math.cos(d) - a.y * Math.sin(d),
    y: a.x * Math.sin(d) + a.y * Math.cos(d),
  });
  const sc = (a, c) => ({ x: a.x * c, y: a.y * c });
  const blend = (c1, c2) =>
    '#' +
    [1, 3, 5]
      .map((k) =>
        Math.round((parseInt(c1.slice(k, k + 2), 16) + parseInt(c2.slice(k, k + 2), 16)) / 2)
          .toString(16)
          .padStart(2, '0')
      )
      .join('');
  const PAIRS = [[0, 1], [0, 2], [0, 3], [1, 2], [1, 3], [2, 3]].map(([i, j]) => ({
    i,
    j,
    color: blend(instruments[i].color, instruments[j].color),
  }));

  const WORLDS = (() => {
    const worlds = [];
    const vals = [0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100];
    let totalP = 0;
    for (const a of vals) for (const b of vals) for (const c of vals) for (const d of vals) {
      const x = [a, b, c, d];
      if (x.every((v) => v === 0)) continue;
      const p = x.reduce((acc, v) => acc * (v === 0 ? 0.8 : 0.02), 1);
      worlds.push({ x, p });
      totalP += p;
    }
    for (const w of worlds) w.p /= totalP;
    return worlds;
  })();

  function zOf(x, ar) {
    let zx = 0, zy = 0;
    for (let i = 0; i < 4; i++) {
      zx += (ar[i].x * x[i]) / 100;
      zy += (ar[i].y * x[i]) / 100;
    }
    return [zx, zy];
  }
  const cell = (z) => `${Math.round(z[0] / RES)}_${Math.round(z[1] / RES)}`;

  function floorOf(ar) {
    const groups = new Map();
    for (const w of WORLDS) {
      const key = cell(zOf(w.x, ar));
      let g = groups.get(key);
      if (!g) {
        g = { p: 0, s: [0, 0, 0, 0], q: [0, 0, 0, 0] };
        groups.set(key, g);
      }
      g.p += w.p;
      for (let i = 0; i < 4; i++) {
        g.s[i] += w.p * w.x[i];
        g.q[i] += w.p * w.x[i] * w.x[i];
      }
    }
    let cost = 0;
    for (const g of groups.values())
      for (let i = 0; i < 4; i++) cost += g.q[i] - (g.s[i] * g.s[i]) / g.p;
    return cost / 100;
  }
  const CROSS_FLOOR = floorOf(cross());

  function oracleFor(t, ar) {
    const k = cell(zOf(t, ar));
    let p = 0;
    const s = [0, 0, 0, 0];
    for (const w of WORLDS) {
      if (cell(zOf(w.x, ar)) === k) {
        p += w.p;
        for (let i = 0; i < 4; i++) s[i] += w.p * w.x[i];
      }
    }
    const guess = s.map((v) => Math.round((10 * v) / p) / 10);
    const cost = guess.reduce((a, g, i) => a + ((g - t[i]) / 10) ** 2, 0);
    return { guess, cost };
  }

  function parasOf(ar) {
    return PAIRS.map(({ i, j, color }) => {
      const a = ar[i], b = ar[j];
      return {
        i,
        j,
        color,
        pts: `0,0 ${a.x},${-a.y} ${a.x + b.x},${-(a.y + b.y)} ${b.x},${-b.y}`,
        flat:
          Math.abs(a.x * b.y - a.y * b.x) < 0.04 &&
          Math.hypot(a.x, a.y) > 0.15 &&
          Math.hypot(b.x, b.y) > 0.15,
      };
    });
  }

  const STRATEGIES = [
    {
      name: 'The naive cross',
      arrows: cross(),
      text:
        'Antipodal pairs share a line, so when both members wake up their messages ' +
        'collide — no decoder can tell which instruments were awake. Both members of a ' +
        'pair wake together on ~4% of rounds (a 20% chance twice over), and all a ' +
        'perfect decoder can do is hedge: the plateau you saw in the Oracle’s guesses ' +
        'is exactly that hedge.',
    },
    {
      name: 'Opened pairs',
      arrows: [
        { x: 1, y: 0 },
        { x: 0, y: 1 },
        rot({ x: -1, y: 0 }, -deg(10)),
        rot({ x: 0, y: -1 }, deg(10)),
      ],
      text:
        'Tilt the pair members apart, and two awake instruments sweep a parallelogram ' +
        'instead of a line — different readings now land on different points. Only a ' +
        'thin sliver of overlap remains, and it takes three instruments awake at once ' +
        '(a 20% chance three times over, ~0.8% of rounds) to land in it. But mind your ' +
        'display: open the pair by less than its resolution and nothing changes at all.',
    },
    {
      name: 'Asymmetric levers',
      arrows: [
        { x: 1, y: 0 },
        { x: 0, y: 1 },
        sc({ x: -1, y: 0 }, 0.25),
        sc({ x: 0, y: -1 }, 0.25),
      ],
      text:
        'Keep the collisions but concentrate them: a short member is read almost ' +
        'exactly, and every collision lands on the long arm’s cheap plateau.',
      caveat:
        'Careful — here we are playing a simplified version of the game: the coarse ' +
        'display rounds the short lever away, so asymmetry alone loses. With a ' +
        'continuous readout it beats symmetry. The best strategy depends on the task!',
    },
    {
      name: 'Opened + asymmetric',
      arrows: [
        { x: 1, y: 0 },
        { x: 0, y: 1 },
        sc(rot({ x: -1, y: 0 }, -deg(25)), 1 / 3),
        sc(rot({ x: 0, y: -1 }, deg(25)), 1 / 3),
      ],
      text:
        'Both dials at once: opening the pair separates the readings of co-awake ' +
        'instruments, asymmetry makes whatever confusion remains cheap. This is the ' +
        'shape of the closed-form optimum family — and what trained deep models ' +
        'actually discover.',
    },
    {
      name: 'The fan',
      arrows: [
        { x: 1, y: 0 },
        rot({ x: 1, y: 0 }, deg(45)),
        rot({ x: 1, y: 0 }, deg(90)),
        rot({ x: 1, y: 0 }, deg(135)),
      ],
      text:
        'Spreading everything evenly looks fair, but it mixes every pair of instruments ' +
        'at once — every region overlaps every other. Uniform superposition is the worst ' +
        'strategy here.',
    },
  ].map((s) => ({ ...s, floor: floorOf(s.arrows), paras: parasOf(s.arrows) }));

  let arrows = cross();
  let mode = 'design'; // design | guess | reveal | done
  let incoming = false;
  let round = 0;
  let total = 0;
  let oracleTotal = 0;
  let best = null;
  let truth = [0, 0, 0, 0];
  let guesses = [0, 0, 0, 0];
  let oracle = null;
  let dragging = null;
  let svgEl;
  let showTheory = false;
  let coRounds = 0;
  let coLoss = 0;
  let oracleCoLoss = 0;

  $: floorNow = floorOf(arrows);
  $: z = zOf(truth, arrows);
  $: zq = [(Math.round(z[0] / RES) || 0) * RES, (Math.round(z[1] / RES) || 0) * RES];
  $: gz = zOf(guesses, arrows);
  $: ext = mode === 'design' ? 1.6 : Math.max(1.6, Math.hypot(zq[0], zq[1]) + 0.3);
  $: paras = parasOf(arrows);
  $: costs = truth.map((t, i) => ((guesses[i] - t) / 10) ** 2);
  $: roundCost = costs.reduce((a, b) => a + b, 0);
  $: nActive = truth.filter((v) => v > 0).length;
  $: chain = arrows.reduce(
    (acc, w, i) => {
      if (truth[i] === 0) return acc;
      const to = { x: acc.p.x + (w.x * truth[i]) / 100, y: acc.p.y + (w.y * truth[i]) / 100 };
      acc.segs.push({ from: acc.p, to, color: instruments[i].color });
      acc.p = to;
      return acc;
    },
    { p: { x: 0, y: 0 }, segs: [] }
  ).segs;

  function roll() {
    let t;
    do {
      t = instruments.map(() =>
        Math.random() < 0.2 ? (1 + Math.floor(Math.random() * 10)) * 10 : 0
      );
    } while (t.every((v) => v === 0));
    truth = t;
    guesses = [0, 0, 0, 0];
    oracle = null;
    mode = 'guess';
    incoming = true;
    setTimeout(() => (incoming = false), 800);
  }
  function startGame() {
    round = 1;
    total = 0;
    oracleTotal = 0;
    coRounds = 0;
    coLoss = 0;
    oracleCoLoss = 0;
    roll();
  }
  function submit() {
    oracle = oracleFor(truth, arrows);
    total += roundCost;
    oracleTotal += oracle.cost;
    if (nActive >= 2) {
      coRounds += 1;
      coLoss += roundCost;
      oracleCoLoss += oracle.cost;
    }
    mode = 'reveal';
  }
  function next() {
    if (round === ROUNDS) {
      best = best === null ? total : Math.min(best, total);
      showTheory = true;
      mode = 'done';
    } else {
      round += 1;
      roll();
    }
  }
  function loadStrategy(s) {
    arrows = s.arrows.map((a) => ({ ...a }));
    mode = 'design';
  }

  function verdict(c) {
    if (c === 0) return '⚡ PERFECT DECODE!';
    if (c < 5) return '🎯 Sharp reading.';
    if (c < 25) return '🌤️ Wobbly, but you kept it together.';
    if (c < 100) return '😬 That one hurt.';
    return '💥 Catastrophic misread!';
  }
  function oracleLine(rc, oc) {
    if (rc < oc - 1e-9) return '🏆 You beat the perfect decoder this round!';
    if (Math.abs(rc - oc) < 0.05) return '🤝 Dead heat with the Oracle.';
    return `🔮 The Oracle paid only ${oc.toFixed(1)}.`;
  }
  function grade(t, o) {
    if (t <= o + 1e-9) return 'S — you ARE the Oracle';
    const r = t / Math.max(o, 0.1);
    if (r < 1.15) return 'A';
    if (r < 1.4) return 'B';
    if (r < 1.8) return 'C';
    if (r < 2.5) return 'D';
    return 'F';
  }

  function toPlane(e) {
    const r = svgEl.getBoundingClientRect();
    return {
      x: ((e.clientX - r.left) / r.width) * 2 * ext - ext,
      y: -(((e.clientY - r.top) / r.height) * 2 * ext - ext),
    };
  }
  function down(i, e) {
    dragging = i;
    e.target.setPointerCapture(e.pointerId);
    e.preventDefault();
  }
  function move(e) {
    if (dragging === null) return;
    let p = toPlane(e);
    const n = Math.hypot(p.x, p.y);
    if (n > 1) p = { x: p.x / n, y: p.y / n };
    arrows[dragging] = p;
  }
  function up() {
    dragging = null;
  }

  function head(tx, ty, s = 0.09) {
    const a = Math.atan2(ty, tx);
    const p = (da) => `${tx - s * Math.cos(a + da)},${-(ty - s * Math.sin(a + da))}`;
    return `${tx},${-ty} ${p(0.5)} ${p(-0.5)}`;
  }
  const fmt = (v) => (v >= 0 ? ' ' : '') + v.toFixed(2);
</script>

<div class="game">
  <svg
    bind:this={svgEl}
    viewBox="{-ext} {-ext} {2 * ext} {2 * ext}"
    on:pointermove={move}
    on:pointerup={up}
  >
    {#each paras as pa}
      {@const hot = mode === 'reveal' && truth[pa.i] > 0 && truth[pa.j] > 0}
      {#if pa.flat}
        <polygon points={pa.pts} class="flat" class:hot />
      {:else}
        <polygon points={pa.pts} class="para" class:hot fill={pa.color} />
      {/if}
    {/each}
    <line class="axis" x1={-ext} y1="0" x2={ext} y2="0" />
    <line class="axis" x1="0" y1={-ext} x2="0" y2={ext} />
    <circle class="disc" cx="0" cy="0" r="1" />
    {#each arrows as w, i}
      <line
        x1="0" y1="0" x2={w.x} y2={-w.y}
        stroke={instruments[i].color} stroke-width="0.035"
        opacity={mode === 'design' ? 1 : 0.55}
      />
      <polygon
        points={head(w.x, w.y)} fill={instruments[i].color}
        opacity={mode === 'design' ? 1 : 0.55}
      />
      <text class="lbl" x={w.x * 1.18} y={-w.y * 1.18} fill={instruments[i].color}>
        {instruments[i].letter}
      </text>
      {#if mode === 'design'}
        <circle class="handle" cx={w.x} cy={-w.y} r="0.15" on:pointerdown={(e) => down(i, e)} />
      {/if}
    {/each}
    {#if mode === 'reveal'}
      {#each chain as s}
        <line
          x1={s.from.x} y1={-s.from.y} x2={s.to.x} y2={-s.to.y}
          stroke={s.color} stroke-width="0.05" stroke-dasharray="0.08 0.06" opacity="0.85"
        />
      {/each}
    {/if}
    {#if (mode === 'guess' && !incoming) || mode === 'reveal'}
      {#key round}
        <g class="msgg">
          <line class="msg" x1="0" y1="0" x2={zq[0]} y2={-zq[1]} stroke-width="0.045" />
          <polygon class="msghead" points={head(zq[0], zq[1], 0.12)} />
        </g>
      {/key}
      <line
        class="guessarrow" x1="0" y1="0" x2={gz[0]} y2={-gz[1]}
        stroke-width="0.035" stroke-dasharray="0.09 0.06"
      />
      <circle class="guessdot" cx={gz[0]} cy={-gz[1]} r="0.055" />
    {/if}
  </svg>

  <div class="panel">
    {#if mode === 'design'}
      <p class="floorbox">
        Confusion floor: <strong>{floorNow.toFixed(1)}</strong> pts/round
        <span class="sub">
          — what a <em>perfect</em> decoder loses to ambiguity alone.
          Naive cross: {CROSS_FLOOR.toFixed(1)}. Drag the arrows and shrink it!
        </span>
      </p>
      <p class="sub">
        Program the station by dragging the arrow tips. Each shaded region is where one
        pair of instruments lands, tinted by mixing that pair's colours; overlaps are
        ambiguity. A <span class="flatnote">red dashed line</span> is a full collision.
      </p>
      <p class="coords coordlist">
        {#each instruments as ins, i}
          <span style="color:{ins.color}">{ins.letter} ({fmt(arrows[i].x)}, {fmt(arrows[i].y)})</span>
        {/each}
      </p>
      <button on:click={() => (arrows = cross())}>Reset to factory setting</button>
      <button class="primary" on:click={startGame}>Deploy the station &amp; play</button>
    {:else if mode === 'guess'}
      <p class="status">
        Round {round}/{ROUNDS} · you {total.toFixed(1)} · 🔮 {oracleTotal.toFixed(1)}
      </p>
      {#if incoming}
        <p class="incoming">📡 Weak signal… receiving…</p>
      {:else}
        <p class="coords msgline">
          Received: ({zq[0].toFixed(1)}, {zq[1].toFixed(1)})
          <span class="yours">yours: ({fmt(gz[0])}, {fmt(gz[1])})</span>
        </p>
        <p class="sub">
          The dashed arrow is what the station would have transmitted if your guesses
          were the true readings — steer it onto the received one.
        </p>
        {#each instruments as ins, i}
          <label style="--c:{ins.color}">
            <span class="name">{ins.name}</span>
            <input type="range" min="0" max="100" step="5" bind:value={guesses[i]} />
            <span class="val">{guesses[i]}</span>
          </label>
        {/each}
        <button class="primary" on:click={submit}>Submit guesses</button>
      {/if}
    {:else if mode === 'reveal'}
      <p class="status">
        Round {round}/{ROUNDS} · you {total.toFixed(1)} · 🔮 {oracleTotal.toFixed(1)}
      </p>
      <p class="verdict">{verdict(roundCost)}</p>
      {#if nActive >= 2}
        <p class="cooc">
          ⚠️ Co-occurrence! {nActive} instruments fired at once — their arrows added up
          into a single point. The lit-up region is where that pair lands.
        </p>
      {/if}
      <p>{oracleLine(roundCost, oracle.cost)}</p>
      <table>
        <tbody>
          <tr><th></th><th>you</th><th>🔮</th><th>truth</th></tr>
          {#each instruments as ins, i}
            <tr style="color:{ins.color}">
              <td>{ins.name}</td><td>{guesses[i]}</td><td>{oracle.guess[i]}</td><td>{truth[i]}</td>
            </tr>
          {/each}
          <tr>
            <td>points lost</td><td>{roundCost.toFixed(1)}</td>
            <td>{oracle.cost.toFixed(1)}</td><td></td>
          </tr>
        </tbody>
      </table>
      <button class="primary" on:click={next}>{round === ROUNDS ? 'Finish' : 'Next round'}</button>
    {:else}
      <p class="verdict">Grade: {grade(total, oracleTotal)}</p>
      <p class="status">
        You lost {total.toFixed(1)} · the Oracle lost {oracleTotal.toFixed(1)} · protocol
        floor ≈ {(floorNow * ROUNDS).toFixed(1)}
      </p>
      {#if best !== null}
        <p>Best this session: {best.toFixed(1)}</p>
      {/if}
      {#if coRounds > 0}
        <p class="cooc">
          Co-occurrence rounds: {coRounds}/{ROUNDS} — they cost you {coLoss.toFixed(1)}
          of your {total.toFixed(1)} points (the Oracle: {oracleCoLoss.toFixed(1)} of
          {oracleTotal.toFixed(1)}). Rare rounds, most of the damage.
        </p>
      {:else}
        <p class="cooc">
          No two instruments fired together this game — the whole difficulty of the
          problem never showed up. Lucky!
        </p>
      {/if}
      <p>
        Decode sharper to catch the Oracle — or redesign your arrows so even the Oracle
        has less to lose. The gallery below shows what theory (and trained neural
        networks) came up with.
      </p>
      <button on:click={startGame}>Rematch</button>
      <button class="primary" on:click={() => (mode = 'design')}>Redesign your arrows</button>
    {/if}
  </div>
</div>

<div class="theorybar">
  <button on:click={() => (showTheory = !showTheory)}>
    {showTheory ? 'Hide' : 'Show'} the strategy gallery
  </button>
</div>

{#if showTheory}
  <div class="theory">
    <h2>The theory: five strategies</h2>
    <p class="sub">
      With every instrument having a 20% chance of waking up, once silent rounds are
      skipped, ~69% of rounds have one instrument awake and ~31% have two or more —
      rare, but they decide the game. Every strategy below is a different answer to the
      same question: where should those rare collisions land, and who pays for them?
      Load one and watch its floor.
    </p>
    <div class="cards">
      {#each STRATEGIES as s}
        <div class="card">
          <svg viewBox="-1.6 -1.6 3.2 3.2">
            {#each s.paras as pa}
              {#if pa.flat}
                <polygon points={pa.pts} class="flat" />
              {:else}
                <polygon points={pa.pts} class="para" fill={pa.color} />
              {/if}
            {/each}
            {#each s.arrows as w, i}
              <line
                x1="0" y1="0" x2={w.x} y2={-w.y}
                stroke={instruments[i].color} stroke-width="0.045"
              />
              <polygon points={head(w.x, w.y, 0.12)} fill={instruments[i].color} />
            {/each}
          </svg>
          <h3>{s.name}</h3>
          <p class="floorline">floor: <strong>{s.floor.toFixed(1)}</strong> pts/round</p>
          <p>{s.text}</p>
          {#if s.caveat}
            <p class="caveat">{s.caveat}</p>
          {/if}
          <button on:click={() => loadStrategy(s)}>Load this protocol</button>
        </div>
      {/each}
    </div>
    <p class="ainote">
      This game is the toy problem from our research on superposition in deep neural
      networks. Trained networks playing it discover exactly these strategies — and each
      architecture settles on the one its own decoder can actually read: shallow
      (quadratic) models stay on the symmetric cross, deeper models open the pairs and
      lean on asymmetric levers, and models whose architecture cannot express asymmetry
      at all sacrifice a feature outright. The more capable the model, the better the
      strategy it finds — deep models reveal better strategies for superposition. For
      where this story began, see
      <a href="https://transformer-circuits.pub/2022/toy_model/index.html"
        >Toy Models of Superposition</a
      >.
    </p>
  </div>
{/if}

<style>
  .game {
    display: flex;
    flex-wrap: wrap;
    gap: 1.5em;
    align-items: flex-start;
    font-family: var(--font-family-sans);
    margin: 2em 0;
  }

  svg {
    flex: 1 1 280px;
    max-width: 420px;
    touch-action: none;
    background: var(--background-body);
    border: 1px solid var(--text-secondary);
    border-radius: 8px;
  }

  .panel {
    flex: 1 1 260px;
  }

  .panel p {
    font-size: 1rem;
    margin: 0 0 1em 0;
  }

  .status {
    font-weight: 700;
  }

  .floorbox strong {
    font-size: 1.3em;
  }

  .sub {
    font-size: 0.85rem;
    color: var(--text-secondary);
  }

  .flatnote {
    color: #d64545;
  }

  .coords {
    font-family: monospace;
    font-size: 0.85rem;
    display: flex;
    flex-wrap: wrap;
    gap: 0.4em 1em;
  }

  .coordlist {
    flex-direction: column;
    gap: 0.15em;
  }

  .msgline {
    font-size: 1.05rem;
    font-weight: 700;
  }

  .incoming {
    font-size: 1.2rem;
    font-weight: 700;
    animation: pulse 0.8s infinite;
  }

  .verdict {
    font-size: 1.2rem;
    font-weight: 700;
  }

  .para {
    opacity: 0.16;
  }

  .para.hot {
    opacity: 0.45;
  }

  .flat {
    fill: none;
    stroke: #d64545;
    stroke-width: 0.025;
    stroke-dasharray: 0.07 0.05;
    opacity: 0.8;
  }

  .flat.hot {
    stroke-width: 0.05;
    opacity: 1;
  }

  .cooc {
    font-weight: 700;
    color: #d64545;
  }

  .theorybar {
    font-family: var(--font-family-sans);
    margin: 0 0 1em 0;
  }

  .axis {
    stroke: var(--text-secondary);
    stroke-width: 0.012;
    opacity: 0.4;
  }

  .disc {
    fill: none;
    stroke: var(--text-secondary);
    stroke-width: 0.012;
    stroke-dasharray: 0.06 0.06;
    opacity: 0.4;
  }

  .lbl {
    font-size: 0.22px;
    text-anchor: middle;
    dominant-baseline: middle;
  }

  .handle {
    fill: var(--text-main);
    opacity: 0.15;
    cursor: grab;
  }

  .msg {
    stroke: var(--text-main);
  }

  .msghead {
    fill: var(--text-main);
  }

  .msgg {
    animation: pop 0.45s cubic-bezier(0.2, 1.6, 0.4, 1);
    transform-origin: 0 0;
  }

  .guessarrow {
    stroke: var(--primary-color);
  }

  .guessdot {
    fill: var(--primary-color);
  }

  .yours {
    color: var(--primary-color);
    margin-left: 0.8em;
  }

  @keyframes pop {
    from {
      opacity: 0;
      transform: scale(0.2);
    }
    to {
      opacity: 1;
      transform: scale(1);
    }
  }

  @keyframes pulse {
    50% {
      opacity: 0.35;
    }
  }

  label {
    display: flex;
    align-items: center;
    gap: 0.6em;
    margin: 0.4em 0;
    font-size: 0.95rem;
  }

  label .name {
    width: 7.5em;
    color: var(--c);
    font-weight: 700;
  }

  label .val {
    width: 2.5em;
    text-align: right;
    font-variant-numeric: tabular-nums;
  }

  input[type='range'] {
    flex: 1;
    accent-color: var(--c);
  }

  table {
    border-collapse: collapse;
    font-size: 0.95rem;
    margin-bottom: 1em;
  }

  th, td {
    padding: 0.15em 0.7em;
    text-align: right;
  }

  th:first-child, td:first-child {
    text-align: left;
    font-weight: 700;
  }

  tr:last-child td {
    border-top: 1px solid var(--text-secondary);
    font-weight: 700;
  }

  button {
    font-family: var(--font-family-sans);
    font-size: 1rem;
    padding: 0.5em 1.1em;
    margin: 0.3em 0.5em 0.3em 0;
    border: 1px solid var(--text-secondary);
    border-radius: 6px;
    background: var(--background-body);
    color: var(--text-main);
    cursor: pointer;
  }

  button.primary {
    background: var(--primary-color);
    border-color: var(--primary-color);
    color: #fff;
  }

  .theory {
    font-family: var(--font-family-sans);
    margin: 1em 0 2em;
  }

  .theory p {
    text-align: justify;
    hyphens: auto;
  }

  .theory h2 {
    font-size: 1.4em;
    margin: 0 0 0.5em 0;
  }

  .cards {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(230px, 1fr));
    gap: 1em;
    margin-top: 1em;
  }

  .card {
    border: 1px solid var(--text-secondary);
    border-radius: 8px;
    padding: 0.9em;
  }

  .card svg {
    display: block;
    width: 140px;
    margin: 0 auto 0.5em;
    border: none;
  }

  .card h3 {
    font-size: 1.05rem;
    margin: 0 0 0.2em 0;
  }

  .card .floorline {
    font-size: 0.85rem;
    color: var(--text-secondary);
    margin: 0 0 0.4em 0;
  }

  .card p {
    font-size: 0.85rem;
    line-height: 1.5;
    margin: 0 0 0.7em 0;
  }

  .card button {
    font-size: 0.85rem;
    padding: 0.35em 0.8em;
  }

  .caveat {
    color: #d64545;
  }

  .ainote {
    font-size: 0.95rem;
    line-height: 1.6;
    margin-top: 1.5em;
  }
</style>
