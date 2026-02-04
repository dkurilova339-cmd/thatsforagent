# thatsforagent
(function autoSpeedrun() {
    const XOR_KEY = "WO_2024_CHALLENGE";

    const decrypt = (str) => {
        try {
            const binary = atob(str);
            let out = "";
            for (let i = 0; i < binary.length; i++) {
                out += String.fromCharCode(binary.charCodeAt(i) ^ XOR_KEY.charCodeAt(i % XOR_KEY.length));
            }
            return JSON.parse(out);
        } catch (e) { return null; }
    };

    const runStep = () => {
        if (window.location.pathname.includes("/finish")) {
            console.log("🎉 Challenge beendet!");
            return;
        }

        const rawData = sessionStorage.getItem("wo_session");
        if (!rawData) return console.log("Warte auf Session-Initialisierung...");

        const session = decrypt(rawData);
        const match = window.location.pathname.match(/step(\d+)/);
        const currentStep = match ? parseInt(match[1]) : 0;
        const code = session.codes[currentStep]; 

        const input = document.querySelector('input[type="text"]');
        const form = document.querySelector('form');

        if (input && form && code) {
            console.log(`Löse Step ${currentStep} mit Code: ${code}`);
    
            const nativeSetter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype, "value").set;
            nativeSetter.call(input, code);
            input.dispatchEvent(new Event('input', { bubbles: true }));
            form.dispatchEvent(new Event('submit', { cancelable: true, bubbles: true }));
        }
    };
    setInterval(runStep, 500);
    console.log("🚀 Speedrun gestartet. Lass den Tab einfach offen.");
})();
