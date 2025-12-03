# file: generate_flag_plane.py
# -----------------------------------------
# Transform a logo into a flag-style texture
# and composite it onto an aircraft tail image.

from PIL import Image, ImageFilter, ImageOps
import numpy as np

def add_flag_wave_effect(img, amplitude=8, wavelength=40):
    """Apply a horizontal flag-like wave distortion."""
    w, h = img.size
    img_np = np.array(img)

    result = np.zeros_like(img_np)
    for y in range(h):
        shift = int(amplitude * np.sin(2 * np.pi * y / wavelength))
        result[y] = np.roll(img_np[y], shift, axi=0)

    return Image.fromarray(result)

def overlay_logo_on_tail(aircraft_img, flag_img, position=(50, 50), opacity=180):
    """Overlay the flag onto the aircraft tail."""
    flag = flag_img.convert("RGBA")
    flag.putalpha(opacity)

    aircraft = aircraft_img.convert("RGBA")
    aircraft.paste(flag, position, flag)
    return aircraft

def main():
    # Load input images
    logo = Image.open("logo.png")
    aircraft = Image.open("aircraft_tail.jpg")  # Provide your aircraft tail image

    # Convert logo into a flag format
    flag_base = ImageOps.contain(logo, (350, 250))
    flag_effect = add_flag_wave_effect(flag_base)

    # Light fabric texture
    texture = Image.new("RGBA", flag_effect.size, (255, 255, 255, 40))
    flag_with_texture = Image.alpha_composite(flag_effect.convert("RGBA"), texture)

    # Composite flag onto aircraft tail
    final = overlay_logo_on_tail(aircraft, flag_with_texture, position=(120, 80), opacity=200)

    # Save output
    final.save("output_flag_aircraft.png")
    print("Saved: output_flag_aircraft.png")

if __name__ == "__main__":
    main()
# -----------------------------------------
