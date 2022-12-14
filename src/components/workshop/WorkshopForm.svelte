<script>
  // props
  export let formEndpoint;
  export let excludeBox; // no box/padding/card around. just the form itless.

  const emailRegex = /(^$|^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$)/;
  const MAIL_TO =
    'mailto:a.sandrina.p@gmail.com?subject=Workshop%20-%20Accessibility%20Fundamentals&body=So,%20I%20was%20checking%20your%20"Accessibility%20Fundamentals"%20workshop%20and...';

  let refFormFeedback = null;
  let isFormSubmitting = false;
  let isFormSubmitted = false;
  let formErrorMsg = '';

  let hasInlineError = false;

  let first_name = '';
  let email_address = '';

  let errors = {
    name: null,
    email: null,
    time: null,
  };

  let slots = $$props.$$slots;


  function handleChange(e, field) {
    const value = e.target.value;
    switch (field) {
      case 'name':
        if (errors.name && !!value) {
          errors.name = '';
        }
        break;
      case 'email':
        if (errors.email && !!value) {
          errors.email = '';
        }
        break;
      default:
        break;
    }
  }

  function handleBlur(e, field) {
    const value = e.target.value;

    switch (field) {
      case 'name':
        if (!value) {
          errors.name = 'Your first name is required.';
        }
        break;
      case 'email':
        if (!value) {
          errors.email = 'Your e-mail is required.';
        }
        break;
      default:
        break;
    }
  }

  async function handleSubmit(event) {
    if (isFormSubmitting) {
      return;
    }
    console.log(first_name, email_address);
    hasInlineError = false;

    if (!first_name) {
      errors.name = 'Your first name is required.';
      hasInlineError = true;
    }

    if (!email_address) {
      errors.email = 'Your e-mail is required.';
      hasInlineError = true;
    } else if (!email_address.match(emailRegex)) {
      errors.email = 'Your e-mail does not seem valid.';
      hasInlineError = true;
    }

    if (hasInlineError) {
      return;
    }

    isFormSubmitting = true;

    try {
      await fetch(formEndpoint, {
        method: 'POST',
        headers: {
          Accept: 'application/json',
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ first_name, email_address, /* form_time */ }),
      });
    } catch (e) {
      console.error('Error on submit!', e);
      formErrorMsg = e.message;
    }
    isFormSubmitting = false;
    isFormSubmitted = true;
  }

  $: if(isFormSubmitted && refFormFeedback) {
    const formFeedback = document.getElementById('formFeedback')
    if(formFeedback) formFeedback.focus()
  }
</script>

<style lang="postcss">
  .p {
    margin-bottom: $spacer-M;

    strong {
      font-weight: 500;
    }
  }

  .card {
    background: var(--bg_1);
    margin: $spacer-XL 0;
    /* max-width: 440px; */
    padding: $spacer-L $spacer-M;
    border-radius: 3px;
    box-shadow: 2px 2px var(--primary_1_smooth);

    display: flex;
    flex-direction: column;
    align-items: center;
    margin-left: auto;
    margin-right: auto;

    @media (--md) {
      padding: $spacer-LM $spacer-L;
    }

    &-title {
      font-size: $font-XL;
      margin-bottom: $spacer-S;
      text-align: center;

      span {
        white-space: nowrap;
      }
    }

    &-txt {
      margin-top: $spacer-S;
      max-width: 28rem;
      text-align: center;
    }
  }

  .feedbackMsg {
    font-size: $font-L2;
    padding: $spacer-M 0 $spacer-M;
  }

  .feedbackTip {
    font-size: $font-M;
    text-align: center;
  }

  .fields-combo {
    display: flex;
    flex-wrap: wrap;
    margin-bottom: $spacer-L;
    width: 100%;

    .field {
      margin: 0 $spacer-M;
      flex: 1;
      min-width: 20rem;
    }
  }

  .field {
    display: block;
    margin-bottom: $spacer-MS;

    &.error {
      .field-input {
        /* .field-textarea { */
        border-color: var(--error);
      }
    }

    &-label {
      display: block;
      font-weight: 500;
    }

    &-tip {
      color: var(--text_1);
      font-size: $font-S;
    }

    /* &-textarea, */
    &-input {
      display: block;
      margin: $spacer-S 0;
      width: 100%;
      max-width: 30rem;
      height: 3.5rem;
      font-size: inherit;
      color: inherit;
      padding: $spacer-S;
      background-color: var(--bg_1);
      border: 1px solid var(--text_1);
      border-radius: 0.3rem;

      &:hover {
        border-color: var(--text_0);
      }

      &:focus {
        box-shadow: 0 0 0 2px var(--primary_1);
        outline: var(--focus_outline);
      }
    }


    &-error {
      display: block;
      font-size: $font-M;
    }

    &-error {
      color: var(--error);
    }

    &-checkbox {
      display: block;
      margin-top: $spacer-S;
    }
  }

  .btn-submit {
    position: relative;
    font-size: $font-L;
    font-weight: 500;
    padding: $spacer-S $spacer-L;
    min-height: 44px;
    border: none;
    text-decoration: none;
    cursor: pointer;
    background: var(--primary_1);
    color: var(--bg_1);
    border-radius: 6px;

    margin: 0 0 $spacer-XS;

    &:hover,
    &:focus {
      outline: none;
      filter: saturate(2);
    }
  }

  .u-danger {
    color: var(--error);
  }

  .u-primary {
    color: var(--primary_1);
  }

  #wsForm {
    margin-top: -150px;
    padding-bottom: 150px;
    display: block;
  }
</style>

{#if !isFormSubmitted}
  <form class:card={!excludeBox} on:submit|preventDefault={handleSubmit}>
    <span id="wsForm"></span>
    <h3 class="f-title card-title">
      Want to join?
      <span class="u-primary">Mark your spot!</span>
    </h3>

    {#if slots}
      {#if slots.default}
        <div class="default">
          <slot />
        </div>
      {/if}
    {:else}
        <p class="card-txt field-tip">
          Be the first to know when it's scheduled with Early Bird price.
        </p>
    {/if}

    <div class="fields-combo">
      <label class="field" class:error={errors.name}>
        <span class="field-label">Your first name</span>
        <input
          type="text"
          class="field-input"
          aria-required="true"
          aria-invalid={!!errors.name}
          aria-describedby="field-name-error"
          on:keyup={e => handleChange(e, 'name')}
          on:blur={e => handleBlur(e, 'name')}
          bind:value={first_name} />
        {#if errors.name}
          <span id="field-name-error" class="field-error" aria-live="assertive">{errors.name}</span>
        {/if}
      </label>

      <label class="field" class:error={errors.email}>
        <span class="field-label">Your e-mail</span>
        <input
          type="text"
          inputmode="email"
          class="field-input"
          aria-required="true"
          aria-invalid={!!errors.email}
          aria-describedby="field-email-error"
          on:keyup={e => handleChange(e, 'email')}
          on:blur={e => handleBlur(e, 'email')}
          bind:value={email_address} />
        {#if errors.email}
          <span id="field-email-error" class="field-error" aria-live="assertive">{errors.email}</span>
        {/if}
      </label>
    </div>

    {#if hasInlineError}
      <span class="sr-only" aria-live="assertive">Submit failed. The form is invalid. Please review the name and e-mail fields</span>
    {/if}


    <button type="submit" aria-disabled={isFormSubmitting} class="btn-submit" aria-label="Subscribe">
      {!isFormSubmitting ? 'Subscribe' : 'Sending...'}
      {#if isFormSubmitting}
        <span class="sr-only" aria-live="assertive">Sending...</span>
      {/if}
    </button>
    <p class="card-txt field-tip">
      Be the first to know when it's scheduled with Early Bird price.
    </p>
  </form>
{:else}
  <div class="card" bind:this={refFormFeedback}>
    {#if !formErrorMsg}
      <span class="sr-only" tabindex="-1" id="formFeedback">Form submitted with success!</span>

      <h2 class="f-title card-title">
        <span class="u-primary">Cool!</span>
        One last thing...
      </h2>
      <p class="p feedbackMsg">
        Go
        <strong>check your inbox</strong>
        to confirm
        <span class="u-nowrap">your e-mail!</span>
      </p>

      <p class="p feedbackTip">
        Didn't receive an e-mail? Reach me
        <a class="u-link" href={MAIL_TO} target="_blank">a.sandrina.p@gmail.com</a>.
      </p>
    {:else}
      <span class="sr-only" tabindex="-1" id="formFeedback">Form failed!</span>

      <h2 class="f-title card-title">
        <span class="u-danger">Ups!</span>
        Something went wrong...
      </h2>
      <p class="p feedbackMsg">Error: {formErrorMsg}</p>
      <p class="p feedbackTip">
        Please send me an e-mail directly at
        <a class="u-link" href={MAIL_TO} target="_blank">a.sandrina.p@gmail.com</a>
        reporting the above error and I'll reserve your spot!
      </p>
    {/if}
  </div>
{/if}
