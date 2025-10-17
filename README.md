import streamlit as st
import random

class NarraNetStoryGenerator:
    """
    NarraNet: Story Continuation and Ending Generator from User Prompts

    This class takes a user prompt and generates a story continuation and ending.
    """

    def __init__(self, style="neutral"):  # Fixed the __init__ method
        self.style = style

    def generate_continuation(self, prompt):
        # Simple logic to continue the story (could be replaced with ML model)
        continuations = [
            "Suddenly, an unexpected event changed everything.",
            "As the sun set, the main character felt a surge of hope.",
            "But a mysterious figure appeared, offering a choice.",
            "The journey grew more perilous, testing their resolve.",
            "A secret from the past resurfaced, altering their path."
        ]
        return f"{prompt} {random.choice(continuations)}"

    def generate_ending(self, continuation):
        # Simple logic to end the story (could be replaced with ML model)
        endings = [
            "In the end, they found what they were looking for.",
            "Peace finally returned, and lessons were learned.",
            "Though the outcome was unexpected, it brought happiness.",
            "Their adventure became a legend told for generations.",
            "The story closed with hope for a brighter future."
        ]
        return f"{continuation} {random.choice(endings)}"

    def generate_story(self, prompt):
        continuation = self.generate_continuation(prompt)
        ending = self.generate_ending(continuation)
        return ending

# Streamlit Web Interface
 def main():
    # Page configuration
    st.set_page_config(
        page_title="NarraNet Story Generator",
        page_icon="📚",
        layout="wide",
        initial_sidebar_state="expanded"
    )
    
    # Custom CSS for better styling
    st.markdown("""
    <style>
    .main-header {
        font-size: 3rem;
        color: #2E86AB;
        text-align: center;
        margin-bottom: 2rem;
    }
    .story-container {
        background-color: #f8f9fa;
        padding: 2rem;
        border-radius: 10px;
        border-left: 5px solid #2E86AB;
        margin: 1rem 0;
    }
    .example-prompt {
        background-color: #e3f2fd;
        padding: 1rem;
        border-radius: 5px;
        margin: 0.5rem 0;
        font-style: italic;
    }
    </style>
    """, unsafe_allow_html=True)
    
    # Main header
    st.markdown('<h1 class="main-header">📚 NarraNet Story Generator</h1>', unsafe_allow_html=True)
    
    # Sidebar for settings
    with st.sidebar:
        st.header("⚙️ Settings")
        
        # Story style selection
        style_options = ["neutral", "fantasy", "mystery", "adventure", "romance"]
        selected_style = st.selectbox(
            "Choose Story Style:",
            style_options,
            index=0
        )
        
        # Additional options
        st.subheader("Options")
        show_process = st.checkbox("Show story building process", value=False)
        
        # Example prompts
        st.subheader("💡 Example Prompts")
        example_prompts = [
            "A young wizard finds a hidden door in the library that leads to another world",
            "The old lighthouse keeper discovered something unusual washed up on the shore",
            "In a world where dreams become reality, she had a nightmare",
            "The last bookstore in the city held a secret that could change everything",
            "A time traveler accidentally changed one small thing in the past"
        ]
        
        for i, prompt in enumerate(example_prompts):
            if st.button(f"Use Example {i+1}", key=f"example_{i}"):
                st.session_state.example_prompt = prompt

    # Main content area
    col1, col2 = st.columns([2, 1])
    
    with col1:
        st.subheader("✍️ Enter Your Story Prompt")
        
        # Use example prompt if selected
        default_prompt = st.session_state.get('example_prompt', '')
        
        user_prompt = st.text_area(
            "Start your story here...",
            value=default_prompt,
            height=100,
            placeholder="Enter the beginning of your story and let NarraNet continue it for you!",
            help="Write the beginning of your story, and our AI will generate a continuation and ending."
        )
        
        # Generate button
        col_btn1, col_btn2, col_btn3 = st.columns([1, 1, 1])
        
        with col_btn2:
            generate_button = st.button("🎲 Generate Story", type="primary", use_container_width=True)
        
        # Clear session state when new prompt is entered
        if 'example_prompt' in st.session_state and user_prompt != st.session_state.example_prompt:
            if user_prompt != default_prompt:
                del st.session_state.example_prompt

    with col2:
        st.subheader("📊 Story Stats")
        if user_prompt:
            word_count = len(user_prompt.split())
            char_count = len(user_prompt)
            
            st.metric("Words in prompt", word_count)
            st.metric("Characters in prompt", char_count)
            
            # Estimated reading time for generated story
            estimated_words = word_count + 25  # Approximate additional words
            reading_time = max(1, estimated_words // 200)  # 200 words per minute
            st.metric("Estimated reading time", f"{reading_time} min")

    # Generate and display story
    if generate_button and user_prompt:
        with st.spinner("🎭 Crafting your story..."):
            # Initialize the generator
            generator = NarraNetStoryGenerator(style=selected_style)
            
            if show_process:
                st.subheader("🔄 Story Building Process")
                
                # Show original prompt
                st.write("**Your Original Prompt:**")
                st.info(user_prompt)
                
                # Show continuation
                continuation = generator.generate_continuation(user_prompt)
                st.write("**After adding continuation:**")
                st.info(continuation)
                
                # Show final story
                final_story = generator.generate_ending(continuation)
                st.write("**Final Story:**")
            else:
                final_story = generator.generate_story(user_prompt)
            
            # Display the generated story
            st.subheader("📖 Your Generated Story")
            st.markdown(f'<div class="story-container">{final_story}</div>', unsafe_allow_html=True)
            
            # Story actions
            col_action1, col_action2, col_action3 = st.columns(3)
            
            with col_action1:
                if st.button("🔄 Generate Another", key="regenerate"):
                    st.experimental_rerun()
            
            with col_action2:
                # Copy to clipboard functionality would require additional JavaScript
                st.button("📋 Copy Story", disabled=True, help="Copy functionality coming soon!")
            
            with col_action3:
                # Download as text file
                st.download_button(
                    label="💾 Download Story",
                    data=final_story,
                    file_name="my_story.txt",
                    mime="text/plain"
                )

    elif generate_button and not user_prompt:
        st.warning("⚠️ Please enter a story prompt before generating!")

    # Footer
    st.markdown("---")
    st.markdown(
        """
        <div style="text-align: center; color: #666;">
            <p>NarraNet Story Generator - Powered by AI 🤖</p>
            <p>Create endless stories with just a prompt!</p>
        </div>
        """,
        unsafe_allow_html=True
    )

if __name__ == "__main__":
    main()
