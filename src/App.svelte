<script>
	import 'bootstrap/dist/css/bootstrap.min.css';
  let cvFile = null;
  let fullname = "";
  let email = "";
  let mobile = "";
  let fileContent = '';
  let fileName = '';

  function handleFileChange(event) {
    cvFile = event.target.files[0];
    if (cvFile) {
      fileName = cvFile.name;
    }
  }

  async function uploadCV() {
    if (cvFile) {
      const base64Data = await toBase64(cvFile);
      const postData = {
        cv: base64Data,
        fullname: fullname,
        email: email,
        mobile: mobile,
        fileContent: fileContent,
        fileName: fileName
      };

      console.log("Entered details:", postData); // Display details in the console

      const response = await fetch('https://api.recruitly.io/api/cvsubmit/bytes?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(postData)
      });

      // Handle the response as needed
    }
  }

  function toBase64(file) {
    return new Promise((resolve, reject) => {
      const reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = () => {
        fileContent = reader.result.split(',')[1];
        resolve(fileContent);
      };
      reader.onerror = error => reject(error);
    });
  }
</script>

<main>
  <h1>Upload CV</h1>
  <input type="text" placeholder="Full Name" bind:value="{fullname}" />
  <input type="email" placeholder="Email" bind:value="{email}" />
  <input type="tel" placeholder="Mobile" bind:value="{mobile}" />
  <input type="file" accept=".pdf,.doc,.docx" on:change={handleFileChange} />

  <button on:click={uploadCV}>Submit</button>
</main>
