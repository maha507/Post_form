<script>
  import 'bootstrap/dist/css/bootstrap.min.css';

  let fullName = '';
  let email = '';
  let mobile = '';
  let cvFile = null;
  let fileContent = '';
  let fileName = '';

  async function handleFileChange(event) {
    cvFile = event.target.files[0];
    fileContent = await toBase64(cvFile);
    fileName = cvFile.name;
  }

  async function uploadCV() {
    if (cvFile && fileContent && fileName) {
      const postData = {
        fullName,
        email,
        mobile,
        fileContent,
        fileName
      };

      try {
        const response = await fetch('https://api.recruitly.io/api/cvsubmit/bytes?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(postData)
        });

        // Handle the response as needed
        if (response.ok) {
          console.log('CV uploaded successfully');
          resetForm();

        } else {
          console.error('Failed to upload CV');
        }
      } catch (error) {
        console.error('Error uploading CV:', error);
      }
    }
  }

  function toBase64(file) {
    return new Promise((resolve, reject) => {
      const reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = () => resolve(reader.result.split(',')[1]);
      reader.onerror = error => reject(error);
    });
  }

  function handleSubmit(event) {
    event.preventDefault();
    // Validate the form fields and submit data if valid
    if (validateForm()) {
      // TODO: Handle form submission
      uploadCV();
    }
  }

  function validateForm() {
    // Validate the form fields
    if (fullName.length === 0 || fullName.length > 28) {
      alert('FullName is required and should be less than or equal to 28 characters.');
      return false;
    }
    if (!validateEmail(email)) {
      alert('Invalid Email format.');
      return false;
    }
    if (!validateMobile(mobile)) {
      alert('Invalid Mobile format.');
      return false;
    }
    return true;
  }

  function validateEmail(email) {
    // Validate the email format using a regular expression
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
  }

  function validateMobile(mobile) {
    // Validate the mobile format using a regular expression
    const mobileRegex = /^(\+?\d+)?$/;
    return mobileRegex.test(mobile);
  }

  function resetForm() {
    fullName = '';
    email = '';
    mobile = '';
    cvFile = null;
    fileContent = '';
    fileName = '';
    document.getElementById('cv').value = '';
  }
  location.reload();
</script>

<main class="container">
  <form on:submit={handleSubmit}>
    <div class="mb-3">
      <label for="fullName" class="form-label">Full Name:</label>
      <input type="text" class="form-control" id="fullName" bind:value={fullName} placeholder="Full Name" required>
    </div>
    <div class="mb-3">
      <label for="email" class="form-label">Email:</label>
      <input type="email" class="form-control" id="email" bind:value={email} placeholder="Email" required>
    </div>
    <div class="mb-3">
      <label for="mobile" class="form-label">Mobile:</label>
      <div class="input-group">
        <span class="input-group-text">+</span>
        <input type="tel" class="form-control" id="mobile" bind:value={mobile} placeholder="Mobile" required maxlength="10" pattern="^\+?\d+$">
      </div>
    </div>
    <div class="mb-3">
      <label for="cv" class="form-label">CV:</label>
      <input type="file" class="form-control" id="cv" accept=".pdf,.doc,.docx" on:change={handleFileChange} placeholder="CV" required>
    </div>
    <button class="btn btn-primary" type="submit" disabled={!fullName || !email || !mobile || !fileName || !fileContent}>Submit</button>
  </form>
</main>
