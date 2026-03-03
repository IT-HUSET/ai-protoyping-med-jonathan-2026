# shadcn/ui – Instructions for Claude

> This file provides Claude with structured knowledge about shadcn/ui to generate correct, idiomatic code. Based on official documentation from ui.shadcn.com (March 2026).

---

## 1. What is shadcn/ui?

shadcn/ui is **not an npm package** – it is a collection of accessible, well-designed React components that are copied directly into your project. You own all the code and can customize freely.

Core principles:

- **Open Code** – Component source is copied in and fully editable.
- **Composition** – All components share a common, composable API.
- **Distribution** – CLI and flat-file schema for installing components.
- **Beautiful Defaults** – Thoughtful design that works out of the box.
- **AI-Ready** – Open code that LLMs can easily read, understand, and generate.

Built on: **TypeScript**, **Tailwind CSS**, **Radix UI** (or Base UI), **React**.

---

## 2. Installation & Setup

### CLI initialization

```bash
# New project (choose Radix or Base UI)
npx shadcn create

# Existing project
npx shadcn init
```

### Adding components

```bash
# Single component
npx shadcn add button

# Multiple at once
npx shadcn add button card dialog input

# All components
npx shadcn add --all
```

### Key utility: the `cn()` helper

```typescript
import { clsx, type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

Use `cn()` to merge and deduplicate Tailwind classes:

```tsx
<div className={cn("p-4 bg-white", isActive && "bg-primary text-primary-foreground")} />
```

---

## 3. components.json

Configuration file at the project root. Created by `npx shadcn init`.

```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "new-york",
  "rsc": true,
  "tsx": true,
  "tailwind": {
    "config": "",
    "css": "app/globals.css",
    "baseColor": "neutral",
    "cssVariables": true,
    "prefix": ""
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils",
    "ui": "@/components/ui",
    "lib": "@/lib",
    "hooks": "@/hooks"
  },
  "iconLibrary": "lucide"
}
```

**Key fields:**

| Field | Description |
|---|---|
| `style` | `"new-york"` is recommended. `"default"` is deprecated. |
| `rsc` | `true` if using React Server Components (Next.js App Router). |
| `tsx` | `true` for TypeScript, `false` for JavaScript (.jsx). |
| `tailwind.cssVariables` | `true` = CSS variables for theming (recommended). |
| `tailwind.baseColor` | `neutral`, `gray`, `zinc`, `stone`, or `slate`. |
| `tailwind.prefix` | Optional prefix for Tailwind classes, e.g. `"tw-"`. |
| `aliases.ui` | Path to UI components, default `@/components/ui`. |
| `iconLibrary` | `"lucide"` (default). |

---

## 4. Theming (CSS Variables)

shadcn/ui uses CSS variables with OKLCH color format and a `background/foreground` convention.

### Naming convention

- `--primary` → background color (`bg-primary`)
- `--primary-foreground` → text color (`text-primary-foreground`)
- The background suffix is omitted in Tailwind classes: `bg-primary`, not `bg-primary-background`.

### globals.css structure (Tailwind v4)

```css
@import "tailwindcss";
@import "tw-animate-css";
@import "shadcn/tailwind.css";

@custom-variant dark (&:is(.dark *));

:root {
  --background: oklch(1 0 0);
  --foreground: oklch(0.145 0 0);
  --primary: oklch(0.205 0 0);
  --primary-foreground: oklch(0.985 0 0);
  --secondary: oklch(0.97 0 0);
  --secondary-foreground: oklch(0.205 0 0);
  --muted: oklch(0.97 0 0);
  --muted-foreground: oklch(0.556 0 0);
  --accent: oklch(0.97 0 0);
  --accent-foreground: oklch(0.205 0 0);
  --destructive: oklch(0.577 0.245 27.325);
  --destructive-foreground: oklch(0.577 0.245 27.325);
  --border: oklch(0.922 0 0);
  --input: oklch(0.922 0 0);
  --ring: oklch(0.708 0 0);
  --radius: 0.625rem;
  --card: oklch(1 0 0);
  --card-foreground: oklch(0.145 0 0);
  --popover: oklch(1 0 0);
  --popover-foreground: oklch(0.145 0 0);
}

.dark {
  --background: oklch(0.145 0 0);
  --foreground: oklch(0.985 0 0);
  /* ... etc */
}

@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-primary: var(--primary);
  --color-primary-foreground: var(--primary-foreground);
  --color-secondary: var(--secondary);
  --color-secondary-foreground: var(--secondary-foreground);
  --color-muted: var(--muted);
  --color-muted-foreground: var(--muted-foreground);
  --color-accent: var(--accent);
  --color-accent-foreground: var(--accent-foreground);
  --color-destructive: var(--destructive);
  --color-destructive-foreground: var(--destructive-foreground);
  --color-border: var(--border);
  --color-input: var(--input);
  --color-ring: var(--ring);
  --color-card: var(--card);
  --color-card-foreground: var(--card-foreground);
  --color-popover: var(--popover);
  --color-popover-foreground: var(--popover-foreground);
  --radius-sm: calc(var(--radius) - 4px);
  --radius-md: calc(var(--radius) - 2px);
  --radius-lg: var(--radius);
  --radius-xl: calc(var(--radius) + 4px);
}
```

### Adding custom colors

```css
:root {
  --warning: oklch(0.84 0.16 84);
  --warning-foreground: oklch(0.28 0.07 46);
}
.dark {
  --warning: oklch(0.41 0.11 46);
  --warning-foreground: oklch(0.99 0.02 95);
}
@theme inline {
  --color-warning: var(--warning);
  --color-warning-foreground: var(--warning-foreground);
}
```

Then use: `className="bg-warning text-warning-foreground"`

### Dark Mode

Use `next-themes` or similar:

```tsx
import { ThemeProvider } from "next-themes"

<ThemeProvider attribute="class" defaultTheme="system">
  {children}
</ThemeProvider>
```

---

## 5. Component Catalog – Import & Usage

All components are imported from `@/components/ui/*`. Never rewrite component internals – customize via props, `cn()`, and CSS variables.

### Form & Input

#### Button

```tsx
import { Button } from "@/components/ui/button"

<Button variant="default">Default</Button>
<Button variant="destructive">Delete</Button>
<Button variant="outline">Outline</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="link">Link</Button>
<Button size="sm">Small</Button>
<Button size="lg">Large</Button>
<Button size="icon"><IconComponent /></Button>
```

#### Button Group

```tsx
import { ButtonGroup, ButtonGroupItem } from "@/components/ui/button-group"

<ButtonGroup>
  <ButtonGroupItem>Option A</ButtonGroupItem>
  <ButtonGroupItem>Option B</ButtonGroupItem>
</ButtonGroup>
```

#### Input

```tsx
import { Input } from "@/components/ui/input"

<Input type="email" placeholder="Email" />
```

#### Input Group

```tsx
import { InputGroup, InputGroupAddon, InputGroupInput } from "@/components/ui/input-group"

<InputGroup>
  <InputGroupInput placeholder="Search..." />
  <InputGroupAddon><SearchIcon /></InputGroupAddon>
</InputGroup>
```

#### Input OTP

```tsx
import { InputOTP, InputOTPGroup, InputOTPSlot } from "@/components/ui/input-otp"

<InputOTP maxLength={6}>
  <InputOTPGroup>
    <InputOTPSlot index={0} />
    <InputOTPSlot index={1} />
    <InputOTPSlot index={2} />
  </InputOTPGroup>
</InputOTP>
```

#### Textarea

```tsx
import { Textarea } from "@/components/ui/textarea"

<Textarea placeholder="Type here..." />
```

#### Checkbox

```tsx
import { Checkbox } from "@/components/ui/checkbox"

<Checkbox checked={isChecked} onCheckedChange={(checked) => setIsChecked(Boolean(checked))} />
```

#### Radio Group

```tsx
import { RadioGroup, RadioGroupItem } from "@/components/ui/radio-group"

<RadioGroup defaultValue="option-1">
  <RadioGroupItem value="option-1" id="r1" />
  <label htmlFor="r1">Option 1</label>
</RadioGroup>
```

#### Select

```tsx
import { Select, SelectTrigger, SelectContent, SelectItem, SelectValue } from "@/components/ui/select"

<Select>
  <SelectTrigger><SelectValue placeholder="Select..." /></SelectTrigger>
  <SelectContent>
    <SelectItem value="1">Option 1</SelectItem>
    <SelectItem value="2">Option 2</SelectItem>
  </SelectContent>
</Select>
```

#### Switch

```tsx
import { Switch } from "@/components/ui/switch"

<Switch checked={enabled} onCheckedChange={setEnabled} />
```

#### Slider

```tsx
import { Slider } from "@/components/ui/slider"

<Slider defaultValue={[50]} max={100} step={1} />
```

#### Calendar

```tsx
import { Calendar } from "@/components/ui/calendar"

<Calendar mode="single" selected={date} onSelect={setDate} />
```

#### Label

```tsx
import { Label } from "@/components/ui/label"

<Label htmlFor="email">Email</Label>
```

#### Field (new in 2025)

```tsx
import { Field, FieldDescription, FieldError, FieldLabel } from "@/components/ui/field"

<Field>
  <FieldLabel htmlFor="username">Username</FieldLabel>
  <Input id="username" placeholder="name" />
  <FieldDescription>Choose a unique username.</FieldDescription>
  <FieldError>Username is already taken.</FieldError>
</Field>
```

Use `FieldGroup` and `FieldSet` for multi-section forms:

```tsx
<FieldSet>
  <FieldLegend>Shipping Address</FieldLegend>
  <FieldGroup>
    <Field>...</Field>
    <Field>...</Field>
  </FieldGroup>
</FieldSet>
```

#### Form (React Hook Form)

```tsx
import { Form, FormField, FormItem, FormLabel, FormControl, FormMessage } from "@/components/ui/form"
import { useForm } from "react-hook-form"

const form = useForm()

<Form {...form}>
  <form onSubmit={form.handleSubmit(onSubmit)}>
    <FormField
      control={form.control}
      name="email"
      render={({ field }) => (
        <FormItem>
          <FormLabel>Email</FormLabel>
          <FormControl><Input {...field} /></FormControl>
          <FormMessage />
        </FormItem>
      )}
    />
  </form>
</Form>
```

### Layout & Navigation

#### Accordion

```tsx
import { Accordion, AccordionItem, AccordionTrigger, AccordionContent } from "@/components/ui/accordion"

<Accordion type="single" collapsible>
  <AccordionItem value="item-1">
    <AccordionTrigger>Section One</AccordionTrigger>
    <AccordionContent>Panel content here.</AccordionContent>
  </AccordionItem>
</Accordion>
```

#### Breadcrumb

```tsx
import { Breadcrumb, BreadcrumbList, BreadcrumbItem, BreadcrumbLink, BreadcrumbPage, BreadcrumbSeparator } from "@/components/ui/breadcrumb"

<Breadcrumb>
  <BreadcrumbList>
    <BreadcrumbItem><BreadcrumbLink href="/">Home</BreadcrumbLink></BreadcrumbItem>
    <BreadcrumbSeparator />
    <BreadcrumbItem><BreadcrumbPage>Current Page</BreadcrumbPage></BreadcrumbItem>
  </BreadcrumbList>
</Breadcrumb>
```

#### Navigation Menu

```tsx
import { NavigationMenu, NavigationMenuList, NavigationMenuItem, NavigationMenuTrigger, NavigationMenuContent, NavigationMenuLink } from "@/components/ui/navigation-menu"

<NavigationMenu>
  <NavigationMenuList>
    <NavigationMenuItem>
      <NavigationMenuTrigger>Menu</NavigationMenuTrigger>
      <NavigationMenuContent>
        <NavigationMenuLink href="/about">About</NavigationMenuLink>
      </NavigationMenuContent>
    </NavigationMenuItem>
  </NavigationMenuList>
</NavigationMenu>
```

#### Tabs

```tsx
import { Tabs, TabsList, TabsTrigger, TabsContent } from "@/components/ui/tabs"

<Tabs defaultValue="tab1">
  <TabsList>
    <TabsTrigger value="tab1">Tab 1</TabsTrigger>
    <TabsTrigger value="tab2">Tab 2</TabsTrigger>
  </TabsList>
  <TabsContent value="tab1">Content 1</TabsContent>
  <TabsContent value="tab2">Content 2</TabsContent>
</Tabs>
```

#### Sidebar

```tsx
import { SidebarProvider, Sidebar, SidebarTrigger, SidebarContent, SidebarInset } from "@/components/ui/sidebar"

<SidebarProvider defaultOpen>
  <Sidebar side="left" collapsible="icon">
    <SidebarTrigger />
    <SidebarContent>Menu content</SidebarContent>
  </Sidebar>
  <SidebarInset>Main content</SidebarInset>
</SidebarProvider>
```

#### Separator

```tsx
import { Separator } from "@/components/ui/separator"

<Separator className="my-4" />
```

#### Scroll Area

```tsx
import { ScrollArea } from "@/components/ui/scroll-area"

<ScrollArea className="h-72 w-full">
  <div>Long content...</div>
</ScrollArea>
```

#### Resizable

```tsx
import { ResizablePanelGroup, ResizablePanel, ResizableHandle } from "@/components/ui/resizable"

<ResizablePanelGroup direction="horizontal">
  <ResizablePanel defaultSize={50}>Left</ResizablePanel>
  <ResizableHandle withHandle />
  <ResizablePanel>Right</ResizablePanel>
</ResizablePanelGroup>
```

#### Pagination

```tsx
import { Pagination, PaginationContent, PaginationItem, PaginationLink, PaginationPrevious, PaginationNext } from "@/components/ui/pagination"

<Pagination>
  <PaginationContent>
    <PaginationItem><PaginationPrevious href="?page=1" /></PaginationItem>
    <PaginationItem><PaginationLink href="?page=1">1</PaginationLink></PaginationItem>
    <PaginationItem><PaginationLink href="?page=2">2</PaginationLink></PaginationItem>
    <PaginationItem><PaginationNext href="?page=2" /></PaginationItem>
  </PaginationContent>
</Pagination>
```

### Overlays & Dialogs

#### Dialog

```tsx
import { Dialog, DialogTrigger, DialogContent, DialogHeader, DialogTitle, DialogDescription, DialogFooter } from "@/components/ui/dialog"

<Dialog>
  <DialogTrigger asChild><Button>Open</Button></DialogTrigger>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Title</DialogTitle>
      <DialogDescription>Description</DialogDescription>
    </DialogHeader>
    <div>Dialog body</div>
    <DialogFooter>
      <Button>Save</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

#### Alert Dialog

```tsx
import { AlertDialog, AlertDialogTrigger, AlertDialogContent, AlertDialogHeader, AlertDialogTitle, AlertDialogDescription, AlertDialogFooter, AlertDialogAction, AlertDialogCancel } from "@/components/ui/alert-dialog"

<AlertDialog>
  <AlertDialogTrigger>Delete</AlertDialogTrigger>
  <AlertDialogContent>
    <AlertDialogHeader>
      <AlertDialogTitle>Are you sure?</AlertDialogTitle>
      <AlertDialogDescription>This action cannot be undone.</AlertDialogDescription>
    </AlertDialogHeader>
    <AlertDialogFooter>
      <AlertDialogCancel>Cancel</AlertDialogCancel>
      <AlertDialogAction>Confirm</AlertDialogAction>
    </AlertDialogFooter>
  </AlertDialogContent>
</AlertDialog>
```

#### Sheet

```tsx
import { Sheet, SheetTrigger, SheetContent, SheetHeader, SheetTitle, SheetDescription } from "@/components/ui/sheet"

<Sheet>
  <SheetTrigger>Open panel</SheetTrigger>
  <SheetContent side="right">
    <SheetHeader>
      <SheetTitle>Title</SheetTitle>
      <SheetDescription>Description</SheetDescription>
    </SheetHeader>
    <div>Panel body</div>
  </SheetContent>
</Sheet>
```

#### Drawer (Vaul)

```tsx
import { Drawer, DrawerTrigger, DrawerContent, DrawerHeader, DrawerTitle, DrawerDescription } from "@/components/ui/drawer"

<Drawer>
  <DrawerTrigger>Open</DrawerTrigger>
  <DrawerContent>
    <DrawerHeader>
      <DrawerTitle>Title</DrawerTitle>
      <DrawerDescription>Description</DrawerDescription>
    </DrawerHeader>
    <div>Content</div>
  </DrawerContent>
</Drawer>
```

#### Popover

```tsx
import { Popover, PopoverTrigger, PopoverContent } from "@/components/ui/popover"

<Popover>
  <PopoverTrigger>Click</PopoverTrigger>
  <PopoverContent>Popover content</PopoverContent>
</Popover>
```

#### Tooltip

```tsx
import { Tooltip, TooltipTrigger, TooltipContent } from "@/components/ui/tooltip"

<Tooltip>
  <TooltipTrigger>Hover</TooltipTrigger>
  <TooltipContent>Tooltip text</TooltipContent>
</Tooltip>
```

> **NOTE:** Ensure `TooltipProvider` exists in your layout, otherwise tooltips may throw errors.

#### Hover Card

```tsx
import { HoverCard, HoverCardTrigger, HoverCardContent } from "@/components/ui/hover-card"

<HoverCard>
  <HoverCardTrigger>Hover here</HoverCardTrigger>
  <HoverCardContent>Detailed info</HoverCardContent>
</HoverCard>
```

#### Context Menu

```tsx
import { ContextMenu, ContextMenuTrigger, ContextMenuContent, ContextMenuItem } from "@/components/ui/context-menu"

<ContextMenu>
  <ContextMenuTrigger><div>Right-click me</div></ContextMenuTrigger>
  <ContextMenuContent>
    <ContextMenuItem>Action 1</ContextMenuItem>
    <ContextMenuItem>Action 2</ContextMenuItem>
  </ContextMenuContent>
</ContextMenu>
```

#### Dropdown Menu

```tsx
import { DropdownMenu, DropdownMenuTrigger, DropdownMenuContent, DropdownMenuItem, DropdownMenuSeparator } from "@/components/ui/dropdown-menu"

<DropdownMenu>
  <DropdownMenuTrigger asChild><Button variant="outline">Menu</Button></DropdownMenuTrigger>
  <DropdownMenuContent>
    <DropdownMenuItem>Profile</DropdownMenuItem>
    <DropdownMenuSeparator />
    <DropdownMenuItem>Log out</DropdownMenuItem>
  </DropdownMenuContent>
</DropdownMenu>
```

#### Menubar

```tsx
import { Menubar, MenubarMenu, MenubarTrigger, MenubarContent, MenubarItem } from "@/components/ui/menubar"

<Menubar>
  <MenubarMenu>
    <MenubarTrigger>File</MenubarTrigger>
    <MenubarContent>
      <MenubarItem>New</MenubarItem>
      <MenubarItem>Open</MenubarItem>
    </MenubarContent>
  </MenubarMenu>
</Menubar>
```

#### Command (cmdk)

```tsx
import { Command, CommandDialog, CommandInput, CommandList, CommandItem, CommandGroup, CommandEmpty } from "@/components/ui/command"

<CommandDialog open={open} onOpenChange={setOpen}>
  <CommandInput placeholder="Search..." />
  <CommandList>
    <CommandEmpty>No results.</CommandEmpty>
    <CommandGroup heading="Suggestions">
      <CommandItem>Action 1</CommandItem>
      <CommandItem>Action 2</CommandItem>
    </CommandGroup>
  </CommandList>
</CommandDialog>
```

### Feedback & Status

#### Alert

```tsx
import { Alert, AlertTitle, AlertDescription } from "@/components/ui/alert"

<Alert variant="destructive">
  <AlertTitle>Error!</AlertTitle>
  <AlertDescription>Something went wrong.</AlertDescription>
</Alert>
```

#### Toast (Sonner)

```tsx
import { Toaster } from "@/components/ui/sonner"
import { toast } from "sonner"

// In layout:
<Toaster />

// Trigger toast:
toast("Saved!")
toast.success("Done!")
toast.error("Failed")
```

#### Progress

```tsx
import { Progress } from "@/components/ui/progress"

<Progress value={66} />
```

#### Spinner (new in 2025)

```tsx
import { Spinner } from "@/components/ui/spinner"

<Spinner />
```

#### Skeleton

```tsx
import { Skeleton } from "@/components/ui/skeleton"

<Skeleton className="h-4 w-32" />
<Skeleton className="h-12 w-12 rounded-full" />
```

#### Badge

```tsx
import { Badge } from "@/components/ui/badge"

<Badge>Default</Badge>
<Badge variant="secondary">Secondary</Badge>
<Badge variant="destructive">Error</Badge>
<Badge variant="outline">Outline</Badge>
```

#### Empty (new in 2025)

```tsx
import { Empty } from "@/components/ui/empty"

<Empty>No results found.</Empty>
```

### Display & Media

#### Avatar

```tsx
import { Avatar, AvatarImage, AvatarFallback } from "@/components/ui/avatar"

<Avatar>
  <AvatarImage src="/avatar.jpg" alt="Name" />
  <AvatarFallback>JD</AvatarFallback>
</Avatar>
```

#### Card

```tsx
import { Card, CardHeader, CardTitle, CardDescription, CardContent, CardFooter } from "@/components/ui/card"

<Card>
  <CardHeader>
    <CardTitle>Card Title</CardTitle>
    <CardDescription>Short description</CardDescription>
  </CardHeader>
  <CardContent>Content</CardContent>
  <CardFooter><Button>Action</Button></CardFooter>
</Card>
```

#### Table

```tsx
import { Table, TableHeader, TableBody, TableRow, TableHead, TableCell } from "@/components/ui/table"

<Table>
  <TableHeader>
    <TableRow>
      <TableHead>Name</TableHead>
      <TableHead>Status</TableHead>
    </TableRow>
  </TableHeader>
  <TableBody>
    <TableRow>
      <TableCell>Anna</TableCell>
      <TableCell>Active</TableCell>
    </TableRow>
  </TableBody>
</Table>
```

#### Carousel (Embla)

```tsx
import { Carousel, CarouselContent, CarouselItem, CarouselPrevious, CarouselNext } from "@/components/ui/carousel"

<Carousel>
  <CarouselContent>
    <CarouselItem>Slide 1</CarouselItem>
    <CarouselItem>Slide 2</CarouselItem>
  </CarouselContent>
  <CarouselPrevious />
  <CarouselNext />
</Carousel>
```

#### Chart (Recharts)

```tsx
import { ChartContainer, ChartTooltip, ChartTooltipContent, ChartLegend, ChartLegendContent } from "@/components/ui/chart"

<ChartContainer config={{ revenue: { label: "Revenue", color: "#2563eb" } }}>
  <LineChart data={data}>
    <Line dataKey="revenue" />
    <ChartTooltip content={<ChartTooltipContent />} />
    <ChartLegend content={<ChartLegendContent />} />
  </LineChart>
</ChartContainer>
```

#### Aspect Ratio

```tsx
import { AspectRatio } from "@/components/ui/aspect-ratio"

<AspectRatio ratio={16 / 9}>
  <img src="/image.jpg" alt="..." className="object-cover w-full h-full" />
</AspectRatio>
```

#### Item (new in 2025)

```tsx
import { Item, ItemContent, ItemDescription, ItemMedia, ItemTitle } from "@/components/ui/item"

<Item>
  <ItemMedia variant="icon"><HomeIcon /></ItemMedia>
  <ItemContent>
    <ItemTitle>Dashboard</ItemTitle>
    <ItemDescription>Overview of your account.</ItemDescription>
  </ItemContent>
</Item>
```

#### Kbd (new in 2025)

```tsx
import { Kbd, KbdGroup } from "@/components/ui/kbd"

<KbdGroup>
  <Kbd>Ctrl</Kbd>
  <Kbd>S</Kbd>
</KbdGroup>
```

### Miscellaneous

#### Collapsible

```tsx
import { Collapsible, CollapsibleTrigger, CollapsibleContent } from "@/components/ui/collapsible"

<Collapsible>
  <CollapsibleTrigger>Show/Hide</CollapsibleTrigger>
  <CollapsibleContent>Hidden content</CollapsibleContent>
</Collapsible>
```

#### Toggle & Toggle Group

```tsx
import { Toggle } from "@/components/ui/toggle"
import { ToggleGroup, ToggleGroupItem } from "@/components/ui/toggle-group"

<Toggle pressed={isOn} onPressedChange={setIsOn}>Bold</Toggle>

<ToggleGroup type="single" defaultValue="bold">
  <ToggleGroupItem value="bold">B</ToggleGroupItem>
  <ToggleGroupItem value="italic">I</ToggleGroupItem>
</ToggleGroup>
```

---

## 6. CLI Commands

| Command | Description |
|---|---|
| `npx shadcn create` | Create new project (choose Radix/Base UI). |
| `npx shadcn init` | Initialize existing project. |
| `npx shadcn add <components>` | Add components. |
| `npx shadcn add --all` | Add all components. |
| `npx shadcn view <item>` | View component before installing. |
| `npx shadcn search <registry>` | Search registries. |
| `npx shadcn migrate rtl [path]` | Migrate to RTL support. |
| `npx shadcn migrate radix [path]` | Migrate to unified radix-ui package. |

### Flags for `init`

- `--template <next|next-monorepo>` – Project template.
- `--base-color <neutral|gray|zinc|stone|slate>` – Base color.
- `--css-variables` / `--no-css-variables` – CSS variables.
- `--src-dir` / `--no-src-dir` – Use src directory.
- `-y` – Skip confirmation prompts.

### Flags for `add`

- `-o, --overwrite` – Overwrite existing files.
- `-a, --all` – Add all components.
- `-p, --path <path>` – Specify installation path.

---

## 7. File Structure

```
src/
├── components/
│   └── ui/              # shadcn components (generated)
│       ├── button.tsx
│       ├── card.tsx
│       ├── dialog.tsx
│       └── ...
├── lib/
│   └── utils.ts         # cn() helper
├── hooks/               # Custom hooks
└── app/
    └── globals.css      # CSS variables and theme settings
```

**Principle:** Keep `components/ui/` for shadcn primitives. Create composed components in `components/` (e.g. `components/dashboard/`, `components/forms/`).

---

## 8. Best Practices

### General usage

1. **Always import from `@/components/ui/`** – never directly from Radix or other primitives in application code.
2. **Use `cn()`** to merge classes, never string concatenation.
3. **Use `asChild`** on trigger components to compose with your own elements:
   ```tsx
   <DialogTrigger asChild>
     <Button variant="outline">Open</Button>
   </DialogTrigger>
   ```
4. **Never rewrite shadcn component internals directly** – create wrapper components if you need extended functionality.
5. **Respect the composition pattern** – each component has clear sub-components (Header, Content, Footer, etc.).

### Styling

1. Use **CSS variables** (not hardcoded colors) for theme compatibility.
2. Use semantic color classes: `bg-primary`, `text-muted-foreground`, `border-input`, etc.
3. Avoid mixing shadcn design tokens with custom hardcoded values.
4. Always test in **both light and dark mode**.

### Forms

1. Use the **Field component** for simple forms without a form library.
2. Use **Form + React Hook Form + Zod** for complex validation.
3. Group fields with **FieldGroup** and **FieldSet** for multi-section forms.

### Accessibility

- All shadcn components build on Radix UI's accessibility primitives.
- Keyboard navigation, ARIA attributes, and focus management work automatically.
- Always provide `DialogTitle` and `DialogDescription` in dialogs.
- Use `Label` linked to inputs via `htmlFor`.

---

## 9. Radix UI vs Base UI

Since December 2025, shadcn/ui supports both **Radix UI** and **Base UI** as underlying primitive libraries. The API remains identical – only the underlying implementation differs:

```tsx
// Works the same regardless of Radix or Base UI
import { Dialog, DialogContent, DialogTrigger } from "@/components/ui/dialog"
```

Choose your primitive library during `npx shadcn create`. The CLI handles the rest.

### Radix imports (new-york style)

Since 2026, the unified `radix-ui` package is used instead of individual `@radix-ui/react-*` packages:

```tsx
// New (recommended)
import { Dialog as DialogPrimitive } from "radix-ui"

// Old (deprecated)
import * as DialogPrimitive from "@radix-ui/react-dialog"
```

Migrate with: `npx shadcn migrate radix`

---

## 10. RTL Support

shadcn/ui has first-class RTL support since January 2026. The CLI automatically converts physical CSS classes to logical equivalents:

- `ml-4` → `ms-4`
- `text-left` → `text-start`
- `left-*` → `start-*`
- `slide-in-from-left` → `slide-in-from-start`

Enable for new project: `npx shadcn init --rtl`

Migrate existing project: `npx shadcn migrate rtl`

---

## 11. Registry & MCP Server

### Registry

shadcn/ui has a registry system for distributing custom component collections. Configure in `components.json`:

```json
{
  "registries": {
    "@company-ui": {
      "url": "https://registry.company.com/ui/{name}.json"
    }
  }
}
```

Install from registry: `npx shadcn add @company-ui/button`

### MCP Server

shadcn/ui offers an MCP server (Model Context Protocol) that lets AI assistants browse, search, and install components via natural language. Works with Claude Code, Cursor, VS Code (Copilot), and more.

---

## 12. Framework Support

shadcn/ui supports:

- **Next.js** (App Router & Pages Router)
- **Vite** (React)
- **Remix**
- **Astro**
- **Laravel** (Inertia)
- **Gatsby**
- **React Router**
- **TanStack Router / TanStack Start**

Installation via `npx shadcn init` with automatic framework detection.

---

## 13. Common Patterns

### Responsive dialog/drawer

Use dialog on desktop, drawer on mobile:

```tsx
const isMobile = useMediaQuery("(max-width: 768px)")

{isMobile ? (
  <Drawer>
    <DrawerTrigger asChild><Button>Open</Button></DrawerTrigger>
    <DrawerContent>...</DrawerContent>
  </Drawer>
) : (
  <Dialog>
    <DialogTrigger asChild><Button>Open</Button></DialogTrigger>
    <DialogContent>...</DialogContent>
  </Dialog>
)}
```

### Data Table with TanStack Table

Combine the `Table` component with `@tanstack/react-table`:

```tsx
import { useReactTable, getCoreRowModel, flexRender } from "@tanstack/react-table"

const table = useReactTable({ data, columns, getCoreRowModel: getCoreRowModel() })

<Table>
  <TableHeader>
    {table.getHeaderGroups().map(headerGroup => (
      <TableRow key={headerGroup.id}>
        {headerGroup.headers.map(header => (
          <TableHead key={header.id}>
            {flexRender(header.column.columnDef.header, header.getContext())}
          </TableHead>
        ))}
      </TableRow>
    ))}
  </TableHeader>
  <TableBody>
    {table.getRowModel().rows.map(row => (
      <TableRow key={row.id}>
        {row.getVisibleCells().map(cell => (
          <TableCell key={cell.id}>
            {flexRender(cell.column.columnDef.cell, cell.getContext())}
          </TableCell>
        ))}
      </TableRow>
    ))}
  </TableBody>
</Table>
```

### Combobox (searchable select)

```tsx
import { Popover, PopoverTrigger, PopoverContent } from "@/components/ui/popover"
import { Command, CommandInput, CommandList, CommandItem, CommandEmpty } from "@/components/ui/command"
import { Button } from "@/components/ui/button"

<Popover>
  <PopoverTrigger asChild>
    <Button variant="outline">{selectedValue || "Select..."}</Button>
  </PopoverTrigger>
  <PopoverContent>
    <Command>
      <CommandInput placeholder="Search..." />
      <CommandList>
        <CommandEmpty>No results.</CommandEmpty>
        {items.map(item => (
          <CommandItem key={item.value} onSelect={() => setValue(item.value)}>
            {item.label}
          </CommandItem>
        ))}
      </CommandList>
    </Command>
  </PopoverContent>
</Popover>
```

### Date Picker

```tsx
import { Popover, PopoverTrigger, PopoverContent } from "@/components/ui/popover"
import { Calendar } from "@/components/ui/calendar"
import { Button } from "@/components/ui/button"
import { format } from "date-fns"

<Popover>
  <PopoverTrigger asChild>
    <Button variant="outline">
      {date ? format(date, "PPP") : "Pick a date"}
    </Button>
  </PopoverTrigger>
  <PopoverContent>
    <Calendar mode="single" selected={date} onSelect={setDate} />
  </PopoverContent>
</Popover>
```

---

## 14. Code Generation Checklist

When generating shadcn/ui code, verify:

- [ ] All imports come from `@/components/ui/`
- [ ] `cn()` is used for dynamic classes
- [ ] `asChild` is used on triggers where appropriate
- [ ] Semantic color classes (`bg-primary`, `text-muted-foreground`) instead of hardcoded values
- [ ] All dialogs include `DialogTitle` + `DialogDescription`
- [ ] `TooltipProvider` exists in layout if tooltips are used
- [ ] Dark mode works (no hardcoded light/dark colors)
- [ ] Forms use the correct pattern (Field or Form+RHF)
- [ ] Component composition structure is respected (Header, Content, Footer)
- [ ] Icons use lucide-react (the default icon library)