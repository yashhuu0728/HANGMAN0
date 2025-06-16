import streamlit as st

# ----- Setup -----
images = {
    "DOG": "dog.gif",
    "SQUIRREL": "squirrel.gif",
    "HORSE": "horse.gif",
    "WOLF": "wolf.gif",
    "ROOSTER": "rooster.gif",
    "FROG": "frog.gif"
}

target_words = list(images.keys())

if 'word_idx' not in st.session_state:
    st.session_state.word_idx = 0
if 'turns' not in st.session_state:
    st.session_state.turns = 6
if 'guessed_letters' not in st.session_state:
    st.session_state.guessed_letters = []

current_word = target_words[st.session_state.word_idx]

# ----- UI Layout -----
st.set_page_config(page_title="Hangman Web", layout="centered")
st.title("🐾 HANGMAN - Web Edition")
st.markdown("Guess the animal shown in the animation. You have 6 attempts!")

# Show image
st.image(images[current_word], use_column_width=True)

# Display current word state (e.g., "_ O _")
masked_word = ' '.join([letter if letter in st.session_state.guessed_letters else '_' for letter in current_word])
st.subheader(f"Word: {masked_word}")

# Show guesses left and guessed letters
st.write(f"Remaining Turns: *{st.session_state.turns}*")
st.write("Guessed Letters: ", ', '.join(st.session_state.guessed_letters))

# Virtual Keyboard
cols = st.columns(13)
alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"

for i, letter in enumerate(alphabet):
    if cols[i % 13].button(letter):
        if letter not in st.session_state.guessed_letters:
            st.session_state.guessed_letters.append(letter)
            if letter not in current_word:
                st.session_state.turns -= 1

# Game Logic
if all(letter in st.session_state.guessed_letters for letter in current_word):
    st.success(f"🎉 You guessed it! The word was: {current_word}")
    if st.button("Next Word"):
        st.session_state.word_idx = (st.session_state.word_idx + 1) % len(target_words)
        st.session_state.guessed_letters = []
        st.session_state.turns = 6
        st.rerun()

elif st.session_state.turns <= 0:
    st.error(f"😢 You're out of turns! The word was: {current_word}")
    if st.button("Try Another Word"):
        st.session_state.word_idx = (st.session_state.word_idx + 1) % len(target_words)
        st.session_state.guessed_letters = []
        st.session_state.turns = 6
        st.rerun()
