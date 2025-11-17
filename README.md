<!doctype html>
<html lang="ko">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>10215우승연 : 해수온난화 체험</title>
  <style>
    :root {
      --bg: #0b1220;
      --card: #121a2b;
      --muted: #9bb0d3;
      --accent: #58a6ff;
      --warn: #ff4747;
      --ok: #22c55e;
      --bar-bg: #1f2a44;
    }
    * { box-sizing: border-box; }
    body {
      margin: 0; font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
      background: radial-gradient(1200px 600px at 70% -10%, #122142 0%, var(--bg) 45%), var(--bg);
      color: white;
    }
    .wrap { max-width: 980px; margin: 32px auto; padding: 0 16px; }
    header { margin-bottom: 16px; }
    header h1 { margin: 0 0 8px; font-size: 28px; }
    header p { margin: 0; color: var(--muted); }
    .panel {
      background: var(--card); border: 1px solid #1f2a44; border-radius: 14px;
      padding: 16px; margin-top: 16px;
      box-shadow: 0 10px 30px rgba(0,0,0,.3), inset 0 0 0 1px rgba(255,255,255,.02);
    }
    .controls { display: flex; flex-wrap: wrap; gap: 8px; align-items: center; }
    .btn {
      cursor: pointer; border: 1px solid #2a3a5e; background: #15213a;
      color: white; padding: 10px 14px; border-radius: 10px; font-weight: 600;
    }
    .btn:hover { background: #1a2a4b; border-color: #425a92; }
    .readout {
      display: inline-flex; align-items: baseline; gap: 6px; font-weight: 700;
      padding: 10px 14px; border-radius: 10px;
      background: #0f1b33; border: 1px solid #2a3a5e;
    }
    .meter { margin-top: 10px; }
    .bar {
      height: 10px; width: 100%; background: var(--bar-bg);
      border-radius: 999px; overflow: hidden;
    }
    .bar > span { display: block; height: 100%; width: 0%; transition: width .35s ease, background .35s ease; }
    .grid { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 12px; margin-top: 12px; }
    .event {
      background: #0f1a2e; border: 1px solid #1f2d4f;
      border-radius: 12px; padding: 12px;
    }
    .event h3 { margin: 0 0 6px; font-size: 16px; }
    .event small { color: var(--muted); }
    /* Toasts */
    .toasts {
      position: fixed; top: 16px; right: 16px;
      display: flex; flex-direction: column; gap: 8px;
      z-index: 9999;
    }
    .toast {
      max-width: 360px; padding: 12px 14px;
      border-radius: 12px; border: 1px solid #4b0f12;
      background: linear-gradient(180deg, rgba(255,71,71,.25), rgba(255,71,71,.12));
      color: white; cursor: pointer;
      box-shadow: 0 10px 30px rgba(255,71,71,.25);
      animation: slideIn .25s ease forwards;
    }
    .toast strong { color: #ffdada; }

    @keyframes slideIn {
      from { opacity: 0; transform: translateY(-8px); }
      to { opacity: 1; transform: translateY(0); }
    }

    /* Modal */
    .modal {
      position: fixed; inset: 0; display: none;
      place-items: center; background: rgba(0,0,0,.55);
      z-index: 10000;
    }
    .modal[open] { display: grid; }

    .modal-card {
      width: min(720px, 92vw);
      max-height: 80vh; overflow: auto;
      background: #0f1a2e; border: 1px solid #2a3a5e;
      border-radius: 14px; padding: 16px;
      box-shadow: 0 20px 60px rgba(0,0,0,.5);
    }

    .modal-head {
      display: flex; align-items: center; justify-content: space-between;
      border-bottom: 1px solid #24365d;
      padding-bottom: 8px; margin-bottom: 10px;
    }

    .close-x {
      background: #1a2746; border: 1px solid #2a3a5e;
      color: white; border-radius: 8px;
      padding: 6px 10px; cursor: pointer; font-weight: 700;
    }

    .modal-list { display: grid; gap: 10px; }
    .modal-item {
      background: #0e1830; border: 1px solid #1f2d4f;
      border-radius: 10px; padding: 10px;
    }
    .modal-item h4 { margin: 0 0 6px; font-size: 16px; }
    .modal-item p { margin: 0; color: var(--muted); }
  </style>
</head>
<body>
  <div class="toasts"></div>

  <div class="wrap">
    <header>
      <h1>10215우승연 : 해수온난화 체험</h1>
      <p>버튼을 눌러 해수 온도를 변화시키고, 변화 따른 문제점을 파악하는 간단한 시뮬레이터</p>
    </header>
    <!-- 시뮬레이터 패널 -->
    <section class="panel">
      <div class="controls" style="justify-content: space-between;">
        <div class="controls">
          <button class="btn" id="down">− 0.1°C</button>
          <button class="btn" id="up">+ 0.1°C</button>
          <button class="btn" id="reset">재설정</button>
        </div>

        <div class="controls">
          <span class="badge">평균 해수 변화</span>
          <span class="readout"><span id="delta">0.0</span>°C</span>
        </div>
      </div>

      <div class="meter">
        <div class="bar"><span id="barFill"></span></div>
      </div>

      <div class="grid" id="eventGrid"></div>
    </section>

    <!-- 안내 패널 -->
    <section class="panel">
      <h2 style="margin:0 0 8px">주의!</h2>
      <ul style="margin-left: 18px; color: var(--muted)">
        <li>과학c 수행평가 자료로, 높은 퀄리티가 아닙니다! 그저 html로 만든 간단한 시뮬레이터이니, 오타등 미세한 실수가 보인다면 눈감아주세요!</li>
        <li>임계치를 넘으면 <strong style="color:#ffd1d1">빨간 경고창</strong>이 뜹니다! (터치로 닫힘)</li>
        <li>
          현상별 상세 설명:
          <button class="btn" id="openDetails" style="margin-left:6px;">문제점 자세히 보기</button>
        </li>
        <li style="margin-top:6px">
          색상 별 위험도:
          <span style="color:hsl(120,85%,55%)">초록=낮음</span> ·
          <span style="color:hsl(60,85%,55%)">노랑=주의</span> ·
          <span style="color:hsl(0,85%,55%)">빨강=매우위험</span>
        </li>
      </ul>
    </section>
  </div>

  <!-- 팝업 (Modal) -->
  <div class="modal" id="detailsModal">
    <div class="modal-card">
      <div class="modal-head">
        <h3>해수 온난화 문제점 정리</h3>
        <button class="close-x" id="closeModal">닫기 ✕</button>
      </div>

      <div class="modal-list" id="modalList">
        <!-- JS 로 동적으로 채워짐 -->
      </div>
    </div>
  </div>
  <script>
    // ---------- 상태 ----------
    const state = {
      delta: 0.0,
      events: [
        {
          id: "bleaching",
          title: "산호 백화현상",
          threshold: 0.5,
          messageLow: "산호가 스트레스를 받아 하얗게 변하기 시작. 해중 경관·어류 서식지에 경고등.",
          messageHigh: "광범위한 산호 폐사 → 관광업 직접 타격, 연안 어초 붕괴로 어획량 하락.",
          escalateAt: 1.0,
          category: "생태",
        },
        {
          id: "hab",
          title: "독성 적조(HAB) 증가",
          threshold: 0.3,
          messageLow: "독성 플랑크톤 증가로 패류에 독소 축적.",
          messageHigh: "해수욕·패류 채취 금지 빈발, 식중독·피부질환 위험 급증.",
          escalateAt: 0.8,
          category: "건강",
        },
        {
          id: "fish",
          title: "어장 북상·어획 불안정",
          threshold: 0.2,
          messageLow: "민감 어종 이동 시작 → 어획 변동성 확대.",
          messageHigh: "주요 어종 서식지 대규모 이동, 연료비·조업거리↑, 수산물 가격 급등.",
          escalateAt: 0.7,
          category: "경제",
        },
        {
          id: "cyclone",
          title: "태풍 강도 증가",
          threshold: 0.4,
          messageLow: "태풍이 더 많은 에너지를 얻어 강해짐. 침수 위험 점증.",
          messageHigh: "초강력 태풍 빈도 증가, 연안 침수·정전 등 생활 불안정↑",
          escalateAt: 0.9,
          category: "재해",
        },
        {
          id: "sink",
          title: "바다의 CO₂ 흡수력 약화",
          threshold: 0.6,
          messageLow: "지구 온난화 완충력 저하.",
          messageHigh: "온실가스 농도 상승 가속 → 폭염·가뭄 등 기후재난 악순환.",
          escalateAt: 1.2,
          category: "생태",
        },
        {
          id: "tourism",
          title: "해양 관광업 타격",
          threshold: 0.5,
          messageLow: "수중 가시성·경관 악화로 스노클링·다이빙 수요 둔화.",
          messageHigh: "산호 경관 붕괴·해파리 증가로 방문객 급감, 지역 경제 직격탄.",
          escalateAt: 0.9,
          category: "경제",
        },
        {
          id: "micro",
          title: "미세플라스틱 노출 증가",
          threshold: 0.4,
          messageLow: "수온 상승으로 분해·마모 가속 → 미세입자 증가 조짐.",
          messageHigh: "환경 중 농도 상승, 인체 섭취 경로 다변화.",
          escalateAt: 1.0,
          category: "건강",
        },
      ],
      fired: new Set(),
      escalated: new Set(),
    };

    // ---------- DOM ----------
    const $delta = document.getElementById("delta");
    const $barFill = document.getElementById("barFill");
    const $grid = document.getElementById("eventGrid");
    const $toasts = document.querySelector(".toasts");

    // ---------- 유틸 ----------
    const fmt = (x) => x.toFixed(1);
    // 위험도(0~1) → hsl(초록→노랑→빨강)
    function riskColor(sev) {
      const s = Math.max(0, Math.min(1, sev));
      const hue = 120 * (1 - s); // 120=초록 → 0=빨강
      return `hsl(${hue}, 85%, 55%)`;
    }

    // ---------- 렌더 ----------
    function render() {
      $delta.textContent = fmt(state.delta);

      // 상단 메인 게이지(최대 1.5°C 기준)
      const pct = Math.max(0, Math.min(100, (state.delta / 1.5) * 100));
      const sevMain = pct / 100;
      $barFill.style.width = pct + "%";
      $barFill.style.background = `linear-gradient(90deg, ${riskColor(sevMain)}, ${riskColor(Math.min(1, sevMain + 0.25))})`;

      // 이벤트 카드
      $grid.innerHTML = "";
      state.events.forEach(ev => {
        const reached = state.delta >= ev.threshold;
        const hasEsc = typeof ev.escalateAt === "number" && !isNaN(ev.escalateAt);

        // 연속형 위험도(0~1)
        let sev = 0;
        if (reached) {
          if (hasEsc) {
            const span = Math.max(0.0001, ev.escalateAt - ev.threshold);
            sev = Math.max(0, Math.min(1, (state.delta - ev.threshold) / span));
          } else {
            sev = Math.max(0, Math.min(1, (state.delta - ev.threshold) / 0.8));
          }
        }
        const widthPct = reached ? (20 + sev * 80) : 20;

        const card = document.createElement("div");
        card.className = "event";
        card.innerHTML = `
          <h3>${ev.title}</h3>
          <small>임계치: <strong>${ev.threshold.toFixed(1)}°C</strong>${
            ev.escalateAt ? ` / 심화: <strong>${ev.escalateAt.toFixed(1)}°C</strong>` : ""
          }</small>
          <div style="height:8px;margin-top:10px;background:#14203a;border-radius:999px;overflow:hidden">
            <span style="
              display:block;height:100%;
              width:${widthPct}%;
              transition: width .35s ease, background .35s ease;
              background:${reached
                ? `linear-gradient(90deg, ${riskColor(sev)}, ${riskColor(Math.min(1, sev + 0.25))})`
                : 'linear-gradient(90deg,#2b395d,#2b395d)'};">
            </span>
          </div>
          <p style="margin:10px 0 0;color:${reached ? 'white' : 'var(--muted)'}">
            ${reached ? (sev >= 1 ? ev.messageHigh : ev.messageLow) : "아직 임계치에 도달하지 않았습니다."}
          </p>
        `;
        $grid.appendChild(card);
      });
    }

    // ---------- 경고창 ----------
    function toast(title, body) {
      const t = document.createElement("div");
      t.className = "toast";
      t.innerHTML = `<strong>주의! ${title}</strong><div style="margin-top:6px">${body}</div>`;

      // 클릭 시 즉시 닫힘
      t.addEventListener("click", () => {
        t.style.transition = "opacity .2s ease, transform .2s ease";
        t.style.opacity = 0; t.style.transform = "translateY(-6px)";
        setTimeout(() => t.remove(), 220);
      });

      $toasts.appendChild(t);

      // 자동 제거
      setTimeout(() => {
        if (!t.isConnected) return;
        t.style.transition = "opacity .2s ease, transform .2s ease";
        t.style.opacity = 0; t.style.transform = "translateY(-6px)";
        setTimeout(() => t.remove(), 220);
      }, 4500);
    }

    // ---------- 임계 체크 ----------
    function checkEvents(prevDelta, newDelta) {
      state.events.forEach(ev => {
        if (prevDelta < ev.threshold && newDelta >= ev.threshold && !state.fired.has(ev.id)) {
          state.fired.add(ev.id);
          toast(ev.title + "!", ev.messageLow);
        }
        if (ev.escalateAt && prevDelta < ev.escalateAt && newDelta >= ev.escalateAt && !state.escalated.has(ev.id)) {
          state.escalated.add(ev.id);
          toast(ev.title + " (심화)!", ev.messageHigh);
        }
      });
    }

    // ---------- 버튼 ----------
    document.getElementById("up").addEventListener("click", () => {
      const prev = state.delta;
      state.delta = Math.min(2.0, +(state.delta + 0.1).toFixed(1));
      checkEvents(prev, state.delta);
      render();
    });
    document.getElementById("down").addEventListener("click", () => {
      const prev = state.delta;
      state.delta = Math.max(0.0, +(state.delta - 0.1).toFixed(1));
      render();
    });
    document.getElementById("reset").addEventListener("click", () => {
      state.delta = 0.0;
      state.fired.clear();
      state.escalated.clear();
      render();
      toast("재설정 완료", "평균해수가 0.0°C로 초기화되었습니다!");
    });

    // ---------- 팝업 ----------
    const $modal = document.getElementById("detailsModal");
    const $modalList = document.getElementById("modalList");
    const $openBtn = document.getElementById("openDetails");
    const $closeBtn = document.getElementById("closeModal");
    const focusTrapSelectors = 'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])';
    let lastFocused = null;

    function openModal() {
      // 내용 채우기
      $modalList.innerHTML = "";
      state.events.forEach(ev => {
        const item = document.createElement("div");
        item.className = "modal-item";
        item.innerHTML = `
          <h4>• ${ev.title}</h4>
          <p>
            <strong>임계치:</strong> ${ev.threshold.toFixed(1)}°C${
              ev.escalateAt ? ` / <strong>심화:</strong> ${ev.escalateAt.toFixed(1)}°C` : ""
            }<br>
            <strong>기본 영향:</strong> ${ev.messageLow}<br>
            <strong>심화 영향:</strong> ${ev.messageHigh}
          </p>
        `;
        $modalList.appendChild(item);
      });

      lastFocused = document.activeElement;
      $modal.setAttribute("open", "");
      $closeBtn.focus();

      document.addEventListener("keydown", escHandler);
      document.addEventListener("keydown", trapHandler);
    }

    function closeModal() {
      $modal.removeAttribute("open");
      document.removeEventListener("keydown", escHandler);
      document.removeEventListener("keydown", trapHandler);
      if (lastFocused) lastFocused.focus();
    }

    function escHandler(e) { if (e.key === "Escape") closeModal(); }

    function trapHandler(e) {
      if (e.key !== "Tab") return;
      const focusables = $modal.querySelectorAll(focusTrapSelectors);
      if (!focusables.length) return;
      const first = focusables[0];
      const last = focusables[focusables.length - 1];
      if (e.shiftKey && document.activeElement === first) {
        e.preventDefault(); last.focus();
      } else if (!e.shiftKey && document.activeElement === last) {
        e.preventDefault(); first.focus();
      }
    }

    // 오버레이 클릭으로 닫기
    $modal.addEventListener("click", (e) => { if (e.target === $modal) closeModal(); });
    $openBtn.addEventListener("click", openModal);
    $closeBtn.addEventListener("click", closeModal);

    // 초기 렌더
    render();
  </script>
</body>
</html>
