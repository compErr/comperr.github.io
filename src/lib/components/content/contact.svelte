<script module>
    export { Contact };
</script>

<script lang="ts">
</script>

{#snippet Contact()}
    <div>
        <ul class="contact-list">
            <li>
                <a href="mailto:josephjitto.k@gmail.com" class="links">
                    Email (josephjitto.k@gmail.com )
                </a>
            </li>
            <li>
                <a
                    href="https://github.com/JJKVIT"
                    target="_blank"
                    class="links"
                >
                    GitHub
                </a>
            </li>
            <li>
                <a
                    href="https://linkedin.com/in/joseph-jitto-7211b5278"
                    target="_blank"
                    class="links"
                >
                    LinkedIn
                </a>
            </li>
        </ul>
    </div>
{/snippet}

<style>
    .contact-list {
        margin-top: 0px;
        margin-bottom: 0px;
        padding-left: 15px;
    }

    .contact-list li {
        padding-bottom: 5px;
    }

    .links {
        color: inherit;
        text-decoration: none;
        position: relative;
        display: inline-block;
        padding-bottom: 0.1rem;
    }

    .links::after {
        content: "";
        position: absolute;
        bottom: 0;
        left: 0;
        width: 100%;
        height: 0.1rem;
        background-color: #c0caf5;

        transform: scaleX(0);
        transform-origin: center;
        transition: transform 300ms cubic-bezier(0.645, 0.045, 0.355, 1);
    }

    .links:hover::after,
    .links:focus::after {
        transform: scaleX(1);
    }
</style>
