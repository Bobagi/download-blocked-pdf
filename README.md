# download-blocked-pdf

Auto-roll all pages:
```bash
(async function scrollInsideViewer() {
  const getScrollableElement = () => {
    const allElements = document.querySelectorAll('*');
    for (const el of allElements) {
      const style = window.getComputedStyle(el);
      const hasScroll = style.overflow === 'auto' || style.overflowY === 'scroll';
      const tallEnough = el.scrollHeight > el.clientHeight + 20;
      if (hasScroll && tallEnough) return el;
    }
    return null;
  };

  const scroller = getScrollableElement();
  if (!scroller) {
    console.error("❌ Nenhum container com rolagem encontrado.");
    return;
  }

  console.log("✅ Iniciando rolagem dentro do viewer...");

  const delay = ms => new Promise(res => setTimeout(res, ms));
  let previous = -1;
  let idleFrames = 0;

  while (idleFrames < 20) {
    scroller.scrollBy(0, 300);
    await delay(500);
    const current = scroller.scrollTop;
    if (current === previous) {
      idleFrames++;
    } else {
      idleFrames = 0;
      previous = current;
    }
  }

  console.log("✅ Rolagem finalizada (atingido fim ou idle)");
})();

```



Download pdf:
```bash
let jspdf = document.createElement( "script" );
jspdf.onload = function () {
let pdf = new jsPDF();
let elements = document.getElementsByTagName( "img" );
for ( let i in elements) {
let img = elements[i];
if (!/^blob:/.test(img.src)) {
continue ;
}
let canvasElement = document.createElement( 'canvas' );
let con = canvasElement.getContext( "2d" );
canvasElement.width = img.width;
canvasElement.height = img.height;
con.drawImage(img, 0, 0,img.width, img.height);
let imgData = canvasElement.toDataURL( "image/jpeg" , 1.0);
pdf.addImage(imgData, 'JPEG' , 0, 0);
pdf.addPage();
}
pdf.save( "download.pdf" );
};
jspdf.src = 'https://cdnjs.cloudflare.com/ajax/libs/jspdf/1.3.2/jspdf.min.js' ;
document.body.appendChild(jspdf);
```


Or, download with TrustedURL:
```bash
let trustedURL;
if (window.trustedTypes && trustedTypes.createPolicy) {
	// Create a trusted policy for the script URL
	const policy = trustedTypes.createPolicy('myPolicy', {
    	createScriptURL: (input) => input
	});
	trustedURL = policy.createScriptURL('https://cdnjs.cloudflare.com/ajax/libs/jspdf/1.3.2/jspdf.min.js');
} else {
	console.warn("Trusted Types are not supported in this browser. Falling back to using a plain URL.");
	trustedURL = 'https://cdnjs.cloudflare.com/ajax/libs/jspdf/1.3.2/jspdf.min.js';
}

let jspdf = document.createElement("script");
jspdf.onload = function () {
	// Create the PDF document once jsPDF is loaded
	let pdf = new jsPDF();
	let elements = document.getElementsByTagName("img");
	for (let i in elements) {
    	let img = elements[i];
    	if (!/^blob:/.test(img.src)) {
        	continue;
    	}
    	let canvasElement = document.createElement('canvas');
    	let con = canvasElement.getContext("2d");
    	canvasElement.width = img.width;
    	canvasElement.height = img.height;
    	con.drawImage(img, 0, 0, img.width, img.height);
    	let imgData = canvasElement.toDataURL("image/jpeg", 1.0);
    	pdf.addImage(imgData, 'JPEG', 0, 0);
    	pdf.addPage();
	}
	pdf.save("download.pdf");
};

// Use the trusted URL as the script source
jspdf.src = trustedURL;
document.body.appendChild(jspdf);
```
