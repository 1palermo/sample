//here is the code sample 
// made a form for the website www.bigblockers.in

here is the code written in the svelte with tailwind css and sanity integrated 

<script>
	  import Title from '$lib/components/titles.svelte';
	  import Navbar from '$lib/components/navbar_desktop.svelte';
	  import Menu from '$lib/components/menu_mobile.svelte';
    import SelectorDt from './Selector_DT.svelte';
    import SelectorCSC from './Selector_CSC.svelte';
    import { fillForm } from '$lib/stores/fetchSanityData';

    import * as z from 'zod';
    import flatpickr from 'flatpickr';
    import { onMount } from 'svelte';
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    let dateInput;

    onMount(() => {
        flatpickr(dateInput, {
            enableTime: true, 
            dateFormat: 'Y-m-d H:i', 
            minDate: 'today', 
            enable: [{from: "2023-12-09",to: "2023-12-12"},{from: "2023-12-01",to: "2023-12-06"}]
        });
    });

    const appointmentSchema = z.object({
      firstName: z.string().min(1),
      lastName: z.string().min(1),
      email: z.string().email(),
      contactNumber: z.string().min(10).max(15),
      appointmentDate: z.string(), 
      location: z.string().refine(value => ['Bangalore', 'Hyderabad'].includes(value)),
      purpose: z.string().min(1),
    });

function validateForm(formData) {
  try {
    appointmentSchema.parse(formData);
    return { isValid: true, error: null };
  } catch (error) {
    return { isValid: false, error };
  }
}

function handleSubmit() {
  const formData = {
    firstName: document.getElementById('firstName').value,
    lastName: document.getElementById('lastName').value,
    email: document.getElementById('email').value,
    contactNumber: document.getElementById('contactNumber').value,
    appointmentDate: dateInput.selectedDates[0].toISOString(), 
    location: document.querySelector('input[name="location"]:checked').value,
    purpose: document.getElementById('purpose').value,
  };

  const validation = validateForm(formData);

  if (validation.isValid) {
    client.create({
      _type: 'appointment',
      ...formData,
    })
      .then(response => {
        dispatch('formSubmitted', { success: true });
      })
      .catch(error => {
        console.error('Error submitting form data to Sanity:', error);

        dispatch('formSubmitted', { success: false, error });
      });
  } else {
    console.error('Form validation failed:', validation.error);

    dispatch('formSubmitted', { success: false, error: validation.error });
  }
}
</script>


<div
	class="h-full bg-cover bg-no-repeat flex justify-center items-center"
	style="background-image: url('/community-initiatives/hero.svg');">
	<div class="w-4/5 m-8 p-6 xl:p-0 lg:ml-10 bg-white rounded-[20px] flex flex-col justify-center">
		<div class="flex  flex-row items-center justify-evenly lg:mt-6">
			<div class="justify-self-start mr-16 block xl:hidden">
				<Menu />
			</div>
			<div class="justify-start lg:self-start lg:pl-12 xl:hidden">
				<a href="/"><img src={'/community-initiatives/logo.svg'} alt="logo" class="logo h-16 lg:h-auto" /></a>
			</div>
			<div class="hidden xl:flex ">
				<Navbar/>
			</div>
			<div class="justify-end pt-8 ml-16 xl:hidden">
				<div class="flex flex-row justify-center block">
					<div class="self-end pt-8">
						<img src="/community-initiatives/cube_box.svg" alt="cube shape img" class="h-6 w-6" />
					</div>
					<div class="self-start">
						<img
							src="/community-initiatives/cube_box.svg"
							alt="cube shape img"
							class=" h-10 w-10"
						/>
					</div>
				</div>
			</div>
		</div>
		
		<div class="pt-5 flex flex-row items-center lg:justify-center lg:mx-10">
			<div class="flex flex-col items-center justify-center lg:space-y-2">
				<div class="self-center">
					<Title content="Appointment form for Citadel" />
				</div>

				<div
					class="py-5 font-normal text-justify text-sm lg:text-xl text-sec-clr lg:mx-10"
				>
                Citadel is the heart of our community; you can meet, network and build together with the founders for a cause by booking an appointment.
				</div>
			</div>
		</div>

        <form action="">
            <!-- <Form/> -->

            <div class="mx-10 flex flex-col">

                <div class="px-8 py-5">
                    <div class="flex flex-row">
                        <img class="scale-75" src="/Main/cube_box.svg" alt="">
                        <p class="text-2xl pl-2 text-neutral-600 text-[22px] font-semibold">Personal Details</p>
                    </div>
            
                    <!-- First and Last Name -->
                    <div class="mx-10  md:grid md:grid-cols-2 pt-4">
                            <div class="mb-4">
                                <label class="block text-gray-700 text-xl font-light mb-2" for="First Name">
                                First Name
                                </label>
                                <input class="bg-stone-50  border-neutral-700 shadow appearance-none border rounded-lg w-3/4 py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="username" type="text" placeholder="Enter you first name here">
                            </div>
            
                            <div class="mb-4">
                                <label class="block text-gray-700 text-xl font-light mb-2" for="Last Name">
                                Last Name
                                </label>
                                <input class="bg-stone-50  border-neutral-700 shadow appearance-none border rounded-lg w-3/4 py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="username" type="text" placeholder="Enter you last name here">
                            </div>
                    </div>
                    <!-- other details -->
                    <div class="mx-10">
                        <SelectorCSC/>
                    </div>           
                        <hr>                        
                </div>
                <div class="px-8 py-5">
                    
                    <div class="h-10 flex flex-row">
                        <img class="scale-75" src="/Main/cube_box.svg" alt="">
                        <p class="text-2xl pl-2 text-neutral-600 text-[22px] font-semibold">Appointment Details</p>
                    </div>
                    
                    <div class="sm:text-lg text-lg font-semibold text-neutral-600 px-10">
                        As of now, our citadel exists in these 2 locations only.
                    </div>
                        
                    <div class="text-lg py-3 px-10">
                        <input class="scale-125" type="radio" id="" name="Bangalore" value="Bangalore">
                        <label for="Bangalore">Bangalore</label>
                        <br>      
                        <input class="scale-125" type="radio" id="" name="Hyderabad" value="Hyderabad">
                        <label for="Hyderabad">Hyderabad</label>
                    </div>

                    <div class="sm:text-xl text-lg font-semibold text-neutral-600 py-3 px-10">
                        Write your Statement Of Purpose (SOP)
                    </div>
                        
                    <div class="px-10">
                        <textarea id="message" rows="4" class="block p-2.5 w-10/12 text-md bg-stone-50  border-neutral-700  text-gray-900  rounded-lg border   " placeholder="We would like to know what you're expecting from the meet!"></textarea>
                    </div>
                    <div class="sm:text-lg text-lg font-semibold text-neutral-600 py-3 px-10">
                        Check the availability of FounderCheck the availability of Founder
                    </div>
            
                        <!-- Selector for Date and Time -->
                    <!-- <div class="px-10">
                        <SelectorDt/>
                    </div> -->
                    <div class="px-10 pb-5">
                        <input
                            type="text"
                            id="appointment-date"
                            bind:this={dateInput}
                            class="block p-2.5 w-10/12 bg-stone-50 border-neutral-700 text-gray-900 rounded-lg border"
                            placeholder="Select a date"
                        />
                    </div>
                    <hr>
                    <div class="px-10 m-3 flex justify-center align-middle text-justify text-neutral-600 text-lg font-semibold tracking-wider">
                        You're almost done! and will receive the confirmation via email ASAP.
                    </div>
                        
                    <button class=" flex w-60 h-12  justify-center align-middle pt-[10px] rounded-lg border border-neutral-800 mx-auto mt-4  hover:bg-blue-100 text-white font-bold " on:click={handleSubmit}>
                        <p class="text-blue-800 text-lg font-semibold">Submit Your Response</p> 
                    </button>
                </div>
            </div>
        </form>
	</div>
</div>




<style>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type=number] {
  -moz-appearance: textfield;
}
</style>